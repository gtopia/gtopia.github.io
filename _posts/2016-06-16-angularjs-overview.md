---
layout: post
title: AngularJS Overview
comments: true
---

---------------  

AngularJS is a JavaScript framework for dynamic web apps. It makes the cmmunication between HTML and JavaScript easier.

Core features:

* Data Binding & Expressions
* Dependency Injection
* Modules
* Controllers
* Services
* Directives
* Filters



## Data Binding & Expressions  
---------------  
[Data binding](https://docs.angularjs.org/guide/databinding) is the bidirectional auto sync of data between the scope and html elements.  
[Expressions](https://docs.angularjs.org/guide/expression) are usually placed in bindings, such as {.{ expression }}. 

Usage:

### ng-model

```html  
    <input type='text' ng-model='greeting'/>
```

### ng-bind vs. expression

- ng-bind: It has poor readability, but users won't see unrendered template.

```html  
    <body>
        <p ng-bind='greeting'></p>
    </body>
```

- {.{ expression }}: It has good readability, but users are likely to see the first loaded template before angualr render it. We could combine with ng-cloak directive to avoid this issue.

```html  
    <body>
        <p> {.{ greeting }} </p>
    </body>

    <body ng-cloak>
        <p> {.{ greeting }} </p>
    </body>
```



## Dependency Injection  
---------------  
Dependency injection improves the modularity and reusability of our code.  

**Please note:**  
When one function is dependent on something, that something must be accessible.

Usage:

```js  
    angular.module('myModule', [])
    .factory('serviceName', ['depService', function(depService) {
        // ...
    }])

    .directive('directiveName', ['depService', function(depService) {
        // ...
    }])

    .filter('filterName', ['depService', function(depService) {
        // ...
    }])

    .controller('controllerName', ['$scope', 'dep1', 'dep2', function($scope, dep1, dep2) {
        // ...
    }]);
```



## Moudles  
---------------  
A module is a container for the different parts of applications, it is used to define services that are reusable by both the HTML document and other modules.  
### How to use a module

```html  
    <div ng-app='myApp'>  
       ...  
    </div>
```


### How to define a module

```javascript  
    var module = angular.module('myApp', ['otherModule'])
```


### How to get an existing module

```javascript  
    var module = angular.module('myApp')
```


### Two phases when running a module
- CONFIG  
    The config phase happens early while the application is still being built. 

- RUN  
    The run phase happens once the module has loaded all of its services and dependencies.

E.g. :  

```javascript  
    var module = angular.module('myModule', []);

    module.config([function() {  
        alert('I run first');  
    }]);

    module.run([function() {  
        alert('I run second');  
    }]);
```



## Controllers  
---------------  
Controllers link the model and the view using the $scope service.  

| Backend data | <---> | Controller | <---> | HTML | <---> | Users |

For memory purposes, controllers are instantiated only when they are needed and discarded when they are not. Because of this, every time we switch a route or reload a page, Angular cleans up the current controller.   

### How to use a controller in HTML

```html  
    <div ng-app='myApp' ng-controller="myController">
        <p>Hello, {.{ name }}</p>
        <p>{.{ greet() }}</p>
    </div>
```

### How to define a controller in JS

```javascript  
    var module = angular.module('myApp', []);
    module.controller('myController', ['$scope', function($scope) {
        $scope.name = 'John';
        $scope.greet = function() {
                return 'Hello, ' + $scope.name;
            };
        }]);
```



## Services  
---------------  
Services provide functionality that can be reused, and also provide a means for keeping data around for the lifetime of an application while they also can be used across different controllers in a consistent manner.  

Here are five ways to create and register services below:  

### Value  
A value can store any service type, such as a string, a number, a function, and object, etc.  

This value of this service can be injected into any controller, filter, or service.  

E.g. :  

```javascript  
    //define a module
    var myModule = angular.module('myModule', []);

    //define a value
    myModule.value('clientId', '123456');

    //define a controller that injects the value
    myModule.controller('myController', ['$scope', 'clientId', function ($scope, clientId) {
        $scope.clientId = clientId;
    }]);
```


### Constant  
The constant is similar to the value, except that its service value is also available during the module’s configuration phase.  

E.g. :  

```javascript  
    myModule.constant('author', 'John');

    myModule.config(['author', function(author) {
        console.log('This app was made by: ' + author); 
    }]);

    myModule.controller('myController', ["author", function (author) {
        this.author = author; 
    }]);
```


### Factory  
The factory is similar to the value, except that it adds abilities below:  
    - Ability to use dependency injection. There is no place for that in a value.  
    - The factory won't build its singleton until it is first injected (lazy initialization).  

E.g. :  

```javascript  
    //define a factory  
    myModule.factory('toUpperCase', ['$filter', function($filter) {
        return function(value) {
            return $filter('uppercase')(value);
        }
    }]);

    //inject the toUpperCase factory
    myModule.controller('myController', ['toUpperCase', function(toUpperCase) {
        this.uppercase = toUpperCase('john');
    }]);
```


### Service  
The service will generate a singleton of an instantiated object.  

E.g. :  

```javascript  
    //define a service
    myModule.service('person', [function() {
        this.first = 'John';
        this.last = 'Jones';
        this.name = function() {
            return this.first + ' ' + this.last;
        };
    }]);

    //inject the person service
    myModule.controller('myController', ['$scope', 'person', function($scope, person) {
        $scope.name = person.name();
    }]);
```


### Provider  
The provider can be injected during a module’s configuration phase.  

E.g. :  

```javascript  
    //define a provider
    myModule.provider('prefix', [function() {
        var prefix = '';
        //setPrefix can be called during the module's config phase
        this.setPrefix = function(value) {
            prefix = value;
        };

        //this special property is required and returns a factory
        this.$get = [function() {
            return function(value) {
                return prefix + value;
            }
        }];
    }]);

    //inject the provider in the config phase
    myModule.config(['prefixProvider', function(prefixProvider) {
        //note that Angular appends 'Provider' to end of the provider name
        prefixProvider.setPrefix('John ');
    }]);

    //inject the provider's factory
    myModule.controller('myController', ['prefix', function(prefix) {
        this.value = prefix('Smith');
    }]);
```



## Directives  
---------------  
Directives are markers on a DOM element that tell AngularJS's HTML compiler to attach a specified behavior to that element or transform the element.   


### Build-in directives
+ ng-disabled
+ ng-checked
+ ng-selected
+ ng-required
+ ng-class
+ ng-if
+ ng-show / ng-hide
+ ng-controller
+ ng-model
+ ng-change
+ ng-repeat
+ ng-options
+ ng-bind / {.{}} + ng-cloak


### How to use a directive
- restrict: 'E'

```html  
    <my-directive></my-directive>
```

- restrict: 'A'  

```html  
    <div my-directive='expression'></div>
```

- restrict: 'C'  

```html  
    <div class='my-directive: expression;'>
```

- restrict: 'M'  

```html  
    <!--directive: my-directive expression-->
```


### How to define a directive

```javascript  
    module.directive('myDirective', [function() { 
        return {
            // Specific directive declaration style, optional, default is 'A'.
            restrict: 'EACM',  
            
            // Default value is 0, the bigger number, the higher priority.
            priority: Number,  
            
            // Decide if terminal the lower priority directives on the curreny element.
            terminal: Boolean, 

            template: String or Template Function:
                function(tElement, tAttrs) {return tplString},
            
            templateUrl: String or Template Function,
                function(tElement, tAttrs) {return tplPathString},
            
            // Default is false, means the template will be inserted into the directive called element as its subelement.
            replace: Boolean, 
            
            // Default is false, when set true, directive will inherit from parent scope and create a new scope object.
            scope: Boolean or Object, 
            
            // When set as true, will transclude the contents of the directive's element. 
            // When set as 'element', will transclude the whole of the directive's element including any lower priority directives on this element.        
            transclude: true or 'element',  
           
            controller: String or function(scope, element, attrs, transclude, ...) 
            { ... },
            
            controllerAs: String,
            
            // Require another directive and inject its controller as the fourth argument to the linking function. 
            require: String or Array, 
            
            // Optional
            link: function(scope, iElement, iAttrs) { ... }, 
            
            compile: function(tElement, tAttrs, transclude) {
                return {
                    pre: function(scope, iElement, iAttrs, controller) { ... },
                    post: function(scope, iElement, iAttrs, controller) { ... }
                }
                //or
                //return function postLink(...) { ... }
            }
        };
    }]);
```
    
**Please note** the following fields:  

- `scope`  
    When `scope` been set as an Object, we usually use a isolate scope to aviod the mutual influence of local and external scope.  
    There are three policys to bind data of local scope and external scope:  
    1. `@ (or @attr)`
        Bind a local scope property to the value of DOM attribute. The local scope could access the variables in external scope.  
    2. `= (or =attr)`
        Bi-directional data binding.  
    3. `& (or &attr)`
        Provides a way to execute an expression in the context of the parent scope.  
- `require`  
    1. `name`
        Locate the required controller on the current element. Throw an error if not found.  
    2. `^name`
        Locate the required controller by searching the element and its parents. Throw an error if not found.  
    3. `^^name`
        Locate the required controller by searching the element's parents. Throw an error if not found.  
    4. `?name`
        Attempt to locate the required controller or pass null to the link function if not found.  
    5. `?^name`
        Attempt to locate the required controller by searching the element and its parents or pass null to the link function if not found.  
    6. `?^^name`
        Attempt to locate the required controller by searching the element's parents, or pass null to the link function if not found.  
- `compile`  
    This field in charge of converting the DOM template.  
    When compile function is set, we could operate the DOM before the directives and real data put into DOM.  
    If both 'compile' and 'link' are set, the return function of 'compile' will acts as link function, and 'link' will be ignored.  
- `link`  
    This field in charge of linking the scope and DOM, set event listener, monitoring the data change and real-time operate DOM.
    Link function will be invoked after template be compiled and linked with scope.


### Some related documents
- [Directives](https://docs.angularjs.org/guide/directive)
- [$compile](https://docs.angularjs.org/api/ng/service/$compile)



## Filters  
---------------  
A filter formats the value of an expression for display to the user.   


### Build-in filters
* currency
* date
* filter (match substring)
* json
* limitTo (limit the length of array or string)
* number (formats a number as text)
* lowercase
* uppercase
* orderby


### How to use a filter

- Use `uppercase` in JS:  

```javascript  
    var module = angular.module('myApp', []);
    module.controller('myController', [
    '$scope', '$filter', function($scope, $filter) {
        $scope.name = 'John';
        $scope.uppercaseName = function() {
            return $filter('uppercase')($scope.name, param1, param2);
        }; 
    }]);
```

- Use `uppercase` in HTML:  

```html  
    <div ng-app='myApp' ng-controller="myController">
        <p>{.{ name | uppercase : param1 : param2 }}</p>
        <p>{.{ uppercaseName() }}</p>
    </div>
```


### How to define a filter
+ Definition a filter:  

```javascript  
    angular.module('reverseFilter', [])
    .filter('reverse', [function() {
        return function(inputStr, isReverse) {
            var i, out = '';

            inputStr = inputStr || '';

            for (i = 0; i < inputStr.length; i++) {
                out = inputStr.charAt(i) + out;
            }

            // conditional based on optional argument
            if (isReverse) {
                out = out.toUpperCase();
            }

            return out;
        };
    }]);
```


+ Use filter in HTML:  

```html  
    {.{ 'Hello, World!' | reverse : true }}
```

+ Use filter in JS:  

```javascript  
    $filter('reverse')('Hello, World!', true);
```




**Please note:** To fix liquid error when run jekyll build, all the '\{\{' in this post's snippets have been replaced with '{.{'.  

