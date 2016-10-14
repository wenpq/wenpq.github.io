> 本文来自https://code.angularjs.org/1.4.13/docs/guide/services

# Services
Angular services是用依赖注入连接在一起的可替换对象，你可以用services来组织和共享代码到你的应用中。

Angular services 是:
- 延迟实例化，Angular仅在应用组件依赖service时，才实例化service。
- 单例，每个组件依赖的service都是由service工厂产生的单例对象。

Angular 提供了一些有用的services（如$http），但是对于大多数应用，你需要创造自己的services。

## 使用Service
使用Angular service，你要给依赖service的组件 (controller, service, filter or directive)添加依赖项，Angular的依赖注入子系统会处理接下来的事情。

html
```
<div id="simple" ng-controller="MyController">
  <p>Let's try this simple notify service, injected into the controller...</p>
  <input ng-init="message='test'" ng-model="message" >
  <button ng-click="callNotify(message);">NOTIFY</button>
  <p>(you have to click 3 times to see an alert)</p>
</div>
```

js
```
angular.
module('myServiceModule', []).
 controller('MyController', ['$scope','notify', function ($scope, notify) {
   $scope.callNotify = function(msg) {
     notify(msg);
   };
 }]).
factory('notify', ['$window', function(win) {
   var msgs = [];
   return function(msg) {
     msgs.push(msg);
     if (msgs.length == 3) {
       win.alert(msgs.join("\n"));
       msgs = [];
     }
   };
 }]);
```
## 创建services
开发者可以在Angular module中，通过注册service名字和service factory函数自由定义自己的service。
service factory函数会产生单例对象或函数做作为service，这个有service返回的对象或函数会被注入到一个依赖该service的组件中（controller, service, filter or directive）

### Registering Services
Services are registered to modules via the Module API. Typically you use the Module factory API to register a service:
```
var myModule = angular.module('myModule', []);
myModule.factory('serviceId', function() {
  var shinyNewServiceInstance;
  // factory function body that constructs shinyNewServiceInstance
  return shinyNewServiceInstance;
});
Note that you are not registering a service instance, but rather a factory function that will create this instance when called.
```
### Dependencies
Services可以有自己的依赖。就像申明controller的依赖一样，你可以在factory函数中申明其依赖项。

For more on dependencies, see the dependency injection docs.

The example module below has two services, each with various dependencies:
```
var batchModule = angular.module('batchModule', []);

/**
 * The `batchLog` service allows for messages to be queued in memory and flushed
 * to the console.log every 50 seconds.
 *
 * @param {*} message Message to be logged.
 */
batchModule.factory('batchLog', ['$interval', '$log', function($interval, $log) {
  var messageQueue = [];

  function log() {
    if (messageQueue.length) {
      $log.log('batchLog messages: ', messageQueue);
      messageQueue = [];
    }
  }

  // start periodic checking
  $interval(log, 50000);

  return function(message) {
    messageQueue.push(message);
  }
}]);

/**
 * `routeTemplateMonitor` monitors each `$route` change and logs the current
 * template via the `batchLog` service.
 */
batchModule.factory('routeTemplateMonitor', ['$route', 'batchLog', '$rootScope',
  function($route, batchLog, $rootScope) {
    return {
      startMonitoring: function() {
        $rootScope.$on('$routeChangeSuccess', function() {
          batchLog($route.current ? $route.current.template : null);
        });
      }
    };
  }]);
  ```
 
In the example, note that:
- The batchLog service depends on the built-in $interval and $log services.
- The routeTemplateMonitor service depends on the built-in $route service and our custom batchLog service.
- 两个services都用了数组符号来申明他们的依赖。
- identifiers在数组中的顺序与参数在factory函数的顺序一致。

### Registering a Service with $provide
你也可以用module的config函数中的$provider服务来注册服务。
```
angular.module('myModule', []).config(['$provide', function($provide) {
  $provide.factory('serviceId', function() {
    var shinyNewServiceInstance;
    // factory function body that constructs shinyNewServiceInstance
    return shinyNewServiceInstance;
  });
}]);
```
This technique is often used in unit tests to mock out a service's dependencies.
