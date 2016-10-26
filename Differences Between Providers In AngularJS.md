Definition of a provider in AngularJS doc:
> A provider is an object with a $get() method. The injector calls the $get method to create a new instance of a service. 
> The Provider can have additional methods which would allow for configuration of the provider.

We can use $provide to register new providers, for example:
```
app.config(function($provide) {
  $provide.provider('greeting', function() {
this.$get = function() {
  return {
   hello: function(name) {
    alert("Hello, " + name);
    };
  }
};
  });
});  
```
In this case, we define a provider named greeting, which can be injected contoller and derictive. Thus, we can use it like that:
```
app.controller('MainController', function($scope, greeting) {
  $scope.onClick = function() {
greeting.hello('Ford Prefect');
  };
}); 
```
What interested is we also can define a provider by factory, service, those are succinct ways. The following code all do the
same thing:
```
// registe by provider, without $provide, not in config
app.provider('greeting', function() {
  this.$get = function() {
   return {
    hello: function(name) {
    alert("Hello, " + name);
    };
  }
  };
});

// registe by factory
app.factory('greeting', function() {
  return {
   hello: function(name) {
      alert("Hello, " + name);
    };
  }
});

// registe by service
app.service('greeting', function(name) {
  this.hello: function(name) {
      alert("Hello, " + name);
    };
});
```
A service is an injectable constructor. You don't need to return object, because AngularJS will create object if service used.  
A factory is an injectable function rather than constructor, so a object have to return.

Actually, the $provide has six methods to create custom providers, but service and factory are most common, the six methods are:
* constant
* value
* service
* factory
* decorator
* provider

Reference: http://blog.xebia.com/differences-between-providers-in-angularjs/
