### Client (Browser/js) Implementation of require/CommonJS loader

Browser/client emulation of CommonJS/node-js's synchronous module loader (require) library.

With a Lite KISS (Keep It Simple Stupid) approach

See nodejs's module loader for background information. (https://nodejs.org/docs/latest/api/modules.html#modules_the_module_object)

#### Supports js,css,html and json loadng.

#### Documentation :-

See this README.md OR jsdoc on-line here [jsdoc requireLite!](http://themuz.github.io/jsdoc/module-requireLite.html).

Also see the related project [themuz/vmrLite](https://github.com/themuz/vmrLite).

#### Samples :-

And samples basic and the standard "todo" app are here. [themuz GitHub io](http://themuz.github.io/).

#### Features :-
- *cache* - Modules are cached after the first time they are loaded
- *sync* - synchronous (i.e wait) by default like nodejs, Make code/dependancies simple/managable.
- *async* - Async with callback, for eager loading.
- *module* - In each module, the "module" free variable is a reference to the object representing the current module being loaded.
- *module.exports* - The object assigned to module.exports will be the require of the requireLite call.
- *encapsulated* - js is encapsulated in the module pattern, no namespace/global pollution. i.e. Variables local to the module will be private
- *no dependancies*

#### Other file types :=

- *json support* - If the url extension is '.json' json will be loaded, and parsed (JSON.parse) and object returned.
- *css support* - If the url extension is '.css', css will be automatically loaded as a window.document.head.link and link's DOM-Element returned.
- *html support* - If the url extension is '.html' the html will be loaded into a div and div's html-Element returned.

#### Usage

    var loadedObject = require(url,[callback]);

loadedObject is whatever is returned by the js, by assigning to module.exports (see below)

#### JS Modules (.js)

In each js module the "module" free variable is a reference to the object representing the current module.

The *module* variable has the following attributes :-

- exports - Object - The object/function being/to-be exported. Assign to module.exports to return your loadedObject. (initalized to {})
- id - String - The identifier assigned to the module
- filename - String - The pathname/url to the module. (e.g. /js/modules/Circle.js)
- loaded - Boolean - Whether or not the module is done loading, or is in the process of loading

In addition the "exports" free variable is also available, corresponds to module.exports and initialized to {}.
If you use the "exports" variable, only defined new properties/methods.
Don't assign to it, as will be lost. If you wish to return
just an object assign to "module.exports" instead.

#### The module wrapper

Before a module's code is executed, It is wrapped with a function that looks like the following:

    (function (exports, require, module, __filename, __dirname) {
        //  Your module code actually lives in here
    });

#### Deferred loading / Cyclic dependencies

If you wish to defer loading dependent modules to run-time (vs load-time) ensure you use the
paramter variable __dirname when loading resouces, as the physical current working directory
may be different.

NOTE: __dirname does NOT include the trailing "/"

If you have cyclic dependencies at load time (does not happen often, but can happen)
e.g. A requires B that requires A. Defer loading of the dependent module to run-time.

e.g

    function MyModule() { etc ... }

    MyModule.doitlater = function() {
        defered_module_loaded=require( __dirname + '/deferred_module.js')
    }


#### Isnt sync slow, with lots of http requests.

NO, Not if  all modules are pre-loaded (as we should do in production mode for performance)
The require is immediate, why code for complex dependences/async code when not needed.

In fact if we can pre load the modules FASTER, i.e. in parallel (subject to dependencies)

Pre-loading can be performed by use of the new static method require.asyncLoadStack
(see asyncLoadStack, buildLoadStackDetails and buildLoadStackDetails for details)

#### Example Module (circle.js)

    var PI = Math.PI;
    var Circle={};

    Circle.area = function (r) {
      return PI * r * r;
    };

    Circle.circumference = function (r) {
      return 2 * PI * r;
    };

    // we could of use the free var "exports" instead of Circle in this example.
    // in which case the below statement, is not required (i.e exports === module.exports)
    module.exports = Circle;


#### Example Usage  (async)

    var  Circle=require('./lib/circle.js');
    alert('The are of a 1" circle is ' + Circle.area(1) + '"')

#### Example Usage  (async)

    require('./lib/circle.js', function(circle) {
      // Do stuff with the "circle", now its loaded
    });

#### Example Bulk Async Preload load

Sample code to load modules at page load.

    <script type="text/javascript">

    window.addEventListener("load",function() {
        var REQUIRE_LOAD_STACK = [
            ["./js/logger.js", "./js/curl.js"],
            ["./model/WatchModel.js", "./model/ConfigModel.js"],
            ["./model/WatchList.js", "./js/WatchEditVM.js"],
            ["./js/WatchListVM.js"],
            ["./js/mainUI.js"] ];

        require.asyncLoadStack( REQUIRE_LOAD_STACK, function() {
            var mainUI=require('js/mainUI');
            mainUI.main();
        });
    });

#### Change log

    2016-02-04 rename module.__filename -> filename, __id -> id for consistency with CommonJS/node-js
    2016-02-04 removed module.__dirname as in-consistent with CommonJS (use require.cwd as a replacement)
    2016-05-15 Added showLoadStack to console.log module dependacies/load order
    2016-05-15 Added asyncLoadStack preload a set of modules for efficiency (see showLoadStack)
    2016-05-29 Removed cwd, now use parameter __dirname to enable runtime loading. (retained cwd for internal use)

#### TODO:-
- Ability to place the app in dev mode, build up the modules/depencancies by running the application. DONE (see showLoadStack)
- Method to load all modules at startup. DONE (see asyncLoadStack)
- Handle cyclic references e.g. A requires B that requires A. DONE (see runtime-loading notes below)
