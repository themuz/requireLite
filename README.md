### Client (Browser/js) Implementation of require/CommonJS loader 

Browser/client emulation of CommonJS/node-js's syncronous module loader (require) library.

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
- *async* - Async with callback, if you must. 
- *module* - In each module, the "module" free variable is a reference to the object representing the current module being loaded.
- *module.exports* - The object assigned to module.exports will be the require of the requireLite call.
- *not just js* - In addition json/css/html is also supported !!
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

#### Deferred loading.

If you wich to defer loading dependent modules to run-time (vs load-time) ensure you save/use the 
requireLite.cwd (Current working directory) property, since the cwd may differ at run-time. 
Use this variable when loading modules at run-time.

e.g

    function MyModule() { etc ... }    

    MyModule.cwd = require.cwd || ''; // This allows js compatability with node-js or browser 

    MyModule.doitlater = function() {
        defered_module_loaded=require(MyModule.cwd + './deferred_module.js')
    }


#### Isnt sync slow, with lots of http requests.

NO, Not if  all modules are pre-loaded (as we should do in production mode for performance)
The require is immediate, why code for complex dependancies/async code when not needed.

In fact if we can pre load the modules FASTER, i.e. in parallel (subject to dependancies)

This pre-loading is my next project, See below the TODO list.


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

#### Change log

    2016-02-04 rename module.__filename -> filename, __id -> id for consistency with CommonJS/node-js
    2016-02-04 removed module.__dirname as in-consistent with CommonJS (use require.cwd as a replacement)


#### TODO:-
- Ability to place the app in dev mode, build up the modules/depencancies by running the application. 
- Tool to wrap all modules, with dependancies into a single unit for production use.
- Handle cyclic references e.g. A requires B that requires A
- if Async scan the js code for require('module.js' .. and load that async 1st.
