---
layout: post
title:  "AngularJS Unit Testing Notes"
date:   2016-08-17 07:00:00 -0500
categories: javascript jasmine angular testing unit-testing
---

We've been doing more Angular work recently, and I've been getting back into it after being mostly away from Angular for a couple years. I never had very much experience with Jasmine tests for anything other than the most trivial or isolated functions. It seemed like a lot of work and magic to get all the bits working together, despite Angular's focus on making everything testable. 

I'm bound to forget again the next time I need it, so I wanted to take this time to record my notes about the different types of objects you may want to test, and the different ways you test them.

This will follow the basic layers of "Controller -> Service -> [Data]", where "[Data]" is probably just an API call using the $http service (by default very mockable, thankfully). This should cover most cases I will need for the immediate future.

1. Testing Controllers
Let's say I have a trivial controller like this, that just populates some scope variables with data returned from a service:
    
{% highlight javascript %}
angular
  .module('myModule')
  .controller('myFirstController', ['$scope', 'businessLogicService',
    function myFirstController($scope, businessLogicService) {
        $scope.games = {
            'name': 'My Games',
            'values': []
        };
        $scope.books = {
            'name': 'My Books',                    
            'values': []
        }            
    };
  
    businessLogicService.getAllData(function (response) {
        $scope.games = response.value.games;
        $scope.books = response.value.books;
    });
}]);
{% endhighlight %}

And I want to have tests to make sure it populates the right parts of the scope. I can inject a mock service like so, and assert that it sets the values on the scope:

{% highlight javascript %}
describe('myFirstController',
    function () {
        var $rootScope,            
            createController,
            businessLogicService;

        beforeEach(function () {
            module('myModule');

            module(function ($provide) {
                $provide.constant('config', config);
            });
        });

        beforeEach(inject(function ($injector) {
            $rootScope = $injector.get('$rootScope');
            
            businessLogicService = $injector.get('businessLogicService');

            var $controller = $injector.get('$controller');

            createController = function () {
                return $controller('myFirstController',
                {
                    '$scope': $rootScope,
                    'businessLogicService': businessLogicService
                });
            };
        }));

    it('should set books and games correctly',
        function () {
            // Arrange
            var mockData = { 
                'books': {
                    'name': 'Mock Books',
                    'values': [{'title': 'Book 1' }, { 'title': 'Book 2' }]
                },
                'games': {
                    'name': 'Mock Games',
                    'values': [{'title': 'Game 1' }, { 'title': 'Game 2' }, , { 'title': 'Game 3' }]
                },
            }; ;
            
            businessLogicService.getAllData = function (func) {
                var response = { value: mockData };
                func(response);
            }

            // Act
            createController();

            // Assert
            expect($rootScope.games).toBe(mockData.books);
            expect($rootScope.games).toBe(mockData.games);
    });
});
{% endhighlight %}

The nice thing is that it isn't doing anything too complex here. The most confusing bits to me were the '$injector' and '$provide' services. The [angular documentation](https://docs.angularjs.org/api/auto/service/$injector) doesn't help at all:

>$injector is used to retrieve object instances as defined by <a href="https://docs.angularjs.org/api/auto/service/$provide">provider</a>, instantiate types, invoke methods, and load modules.

Oh, I... see. Maybe there is a useful example I can refer to:

{% highlight javascript %}
var $injector = angular.injector();
expect($injector.get('$injector')).toBe($injector);
expect($injector.invoke(function($injector) {
  return $injector;
})).toBe($injector);
{% endhighlight %}

Of course. Now it makes sense. $injector.
Maybe if I look up [$provide](https://docs.angularjs.org/api/auto/service/$provide)?

>The $provide service has a number of methods for registering components with the $injector.

Haha. Ugh. Nobody said it would be easy!

So from a unit testing perspective, these two things work together to let you mock out dependencies. So if I have a controller that needs some configuration value/options called 'config':

{% highlight javascript %}

angular.module('myModule').controller('myFirstController', ['$scope', 'config', function myFirstController($scope, config) {
...
}]);
{% endhighlight %}

I can have the $injector, which is called for me, provide my stubbed config data by setting it up through $provider in the Jasmine test:

{% highlight javascript %}

describe('myFirstController',
    function () {
        var config = { configValue: 'stubConfigValue' };
        ...
        beforeEach(function () {
            ...
            module(function ($provide) {
                $provide.constant('config', config);
            });
            ...
        });
    });
{% endhighlight %}

So now when my controller is created when running this test, it will get my 'config' variable passed in. Definitely useful, but not intuitive if you're as used to more traditional server-side mocking frameworks.

I'll write another post later about testing the service in isolation, which will involve testing its interactions with the $http service provided by Angular. Fortunately there is a lot of documentation already out there to get started as this is a pretty common need.