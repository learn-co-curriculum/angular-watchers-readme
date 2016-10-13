# $digest cycle and dirty-checking

## Overview

We've talked about the digest cycle, but we don't know why it actually might slow down a bit. Let's take a look at why.

## Objectives

- Describe the $digest cycle
- Describe $$watchers

## $$watchers

Whenever we need to watch a model value (such as using `$scope.$watch` or `{{ ctrl.value }}`), Angular pushes an item into a property named `$$watchers` on our scope.

What is a `$$watcher`? A watcher stores the function that we use to get the latest value of the item (remember the first function we passed through to `$scope.$watch`?), the last known value (the `oldValue` that got passed to our callback), the callback, and also whether or not we're deep watching the collection.

When we run the digest cycle, Angular loops through all of our watchers and executes the first function, which returns the latest value of the item. It then compares (or deep checks) the last value and the new value, calling the callback when the values are different.

Imagine we have an `ng-repeat` repeating a list of people with five different expressions displayed in the element: name, email, phone, address, and nickname. When we have 1000 people, we have 1000 * 5 = 5000 watchers added to the `$$watchers` array. These are then **all** run when we run the digest cycle. Can you see how it gets so slow now?

It's important that we try to minimize our `$$watchers` count. Use `$scope.$watch` only when you need to, and try to avoid watching full objects, as it takes more time to compare them.
