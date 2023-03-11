---
title: 📚 Easy Roles & Permissions
long_title: Easy Roles & Permissions in Angularjs Using Cucumber Tag Expressions
layout: blog
categories: 
    - angular 
    - javascript 
    - roles 
    - auth 
    - permissions
---

```js
const parse = require('cucumber-tag-expressions').default;

(function() {
    angular.module('my.app').directive('guard', GuardDirective);
    GuardDirective.$inject = ['UserService'];

    function GuardDirective(UserService) {
        return {
            restrict: 'A',
            link: function(scope, element, attrs) {
                element.hide();
                const expressionNode = parse(attrs.guard);
                UserService.getPermissions()
                    .then((permissions) => expressionNode.evaluate(permissions))
                    .then((isAllowed) => {
                        isAllowed ? element.show(): element.hide();
                    });
            }
        }
    }
})();
```
As it uses the tag expressions parser from cucumber, you can use that syntax to define your permission rules. e.g. `(@manager or @customer) and (not @developer)` as you can see from above most scenarios that you would encounter can be expressed.


You can use the directive to hide items based on permissions without adding the complexity to your contollers or services. Users cannot take actions that they cannot see. Transforming the problem into a view issue. 

Using our GuardDirective we can write code like the following:

```html
<ul class="nav nav-tabs">
    <li>
        <a ui-sref="...">Home</a>
    </li>
    <li guard="VIEW_ADMIN_PANEL">
        <a ui-sref="...">Admin Panel</a>
    </li>
    <li guard="VIEW_ADMIN_PANEL and VIEW_PAYMENT_DATA">
        <a ui-sref="...">Payment Panel</a>
    </li>
</ul>
```

By relying on the cucumber tag expressions parser we can also write more complex rules like the following:

```html
<li guard="VIEW_ADMIN_PANEL and not CRITICAL_CUSTOMER and IN_EARLY_ADOPTER_DEMO_GROUP">
    <a ui-sref="...">Admin Panel</a>
</li>
```

In the above case you can see that we mix auth and feature toggles, treating them all as permissions.

