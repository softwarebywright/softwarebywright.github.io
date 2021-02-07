---
title: "Logical Operators \"AND\" LJTT #6"
description: Let's Learn About The AND Operator
image: /posts/javascript.png
date: Tue Sep 13 2019 21:30:04
subject: Logical AND Operator
specFile: logical-and-operator.spec.js
---

# {{$page.title}}

Welcome back to the series, today we will be working through JavaScript `{{$page.frontmatter.subject}}`.

::: tip
Be sure to check out the first part of the series [here](/blog/posts/learn-js-through-testing/00-getting-started.html) for project set up, or download the project template [here](https://github.com/softwarewright/learn-js-thru-testing/archive/relational-operators.zip). Run `npm install` inside of the project folder to get started if you download it.
:::

**Before getting started be sure to create a `{{$page.frontmatter.specFile}}` file in your project directory. This is where we will put all of the new `Test Cases`.**

## Logical Operators

Logical Operators in JavaScript give us the ability to validate that multiple or single boolean statements or variables are either true or false. There are three logical operators in JavaScript, which are listed below:

|Operator|Operator Name| Description|
|:-------:|:-------:|:-------:|
| && | AND | The `AND` operator checks that a combination of boolean expressions or variables resolve to a truthy value. If this is the case the result will be truthy, otherwise, it will return a falsy value.|
| \|\| | OR | The `OR` operator checks that one of a combination boolean expressions or variables resolve to a truthy value. If this is the case the result will be truthy otherwise it will return a falsy value|
| ! | NOT/BANG | The `NOT` operator negates a boolean expression or variable, therefore if the result of the expression would have been true, the usage of the not operator would return a result of false and vice versa.|

::: tip
I realize that we have not talked about truthy/falsy values, we will cover them in detail down below.
:::

Great, with the general knowledge out of the way we can move into application starting with the `AND` operator.

### AND &&

Let's start by breaking down the description of the `AND` operator, it `checks that combination of boolean expressions or variables resolves to a truthy value`. The first thing for us to consider is what is a boolean expression? Think back to the previous two articles about [equality](/blog/posts/learn-js-through-testing/04-equality-operators) and [relational](/blog/posts/learn-js-through-testing/05-relational-operators) operators, where these are just expressions that evaluate to `true` or `false`. 

A few examples of `Boolean Expressions` can be seen below:

``` javascript
3 < 10 // true
3 > 2 // true
```

``` javascript
var x = "1";
x === 1 // true

var y = 100;
y < 25 // false
```

So what does it look like to use a boolean expression with the `AND` operator? I'm glad you asked! Let's take the first two examples of mentioned above to see what the `AND` operator looks like in action.

``` javascript
3 < 10 && 3 > 2
```

Easy enough right? Both of the `Boolean Expressions` are `true` statements therefore when using the `AND` operator we `expect` the result of the entire statement `to be true`. The next question is how do we prove this in a `Test Case` with assertions? Like the other tutorials, we'll start with the `Test Case` description, the description in the table above seems to fit: `the AND operator checks that a combination of boolean expressions or variables resolve to a truthy`

``` javascript
// test case description goes here
test("the AND operator checks that a combination of boolean expressions or variables resolve to a truthy value", () => {
 // assertions go here
})
```

Now let's add our `AND` example to a `Test Assertion` where we `expect` `3 < 10 && 3 > 2` `toBeTrue`, because both `Boolean Expressions` are true.

``` javascript {4}
// test case description goes here
test("the AND operator checks that a combination of boolean expressions or variables resolve to a truthy value", () => {
 // assertions go here
 expect(3 < 10 && 3 > 2).toBeTrue();
})
```

Give your `Test Cases` a run with the `npm test` command in your terminal, and let's move on to the next example.

There is a very important second piece of the `Test Case` description that we have not covered, the use of variables. How can we substitute a variable for a `Boolean Expression`? Let's see down below.

``` javascript {2}
var x = 25;
x && x > 10
```

Above we create a variable assigned the value `25` and follow it with the usage of itself, the `AND` operator, and a `Boolean Expression` stating that the value of x is greater than `10`. When a variable used with the `AND` operator instead of a `Boolean Expression` something very interesting will happen, JavaScript treats values as if they are `Boolean` values. This is where the concept of `truthy` and `falsy` values come into play.

#### Truthy vs Falsy Values

In JavaScript every value has two values, the actual value e.g. `25` and then there is its inherent `Boolean` value. In the case of the value of `25` its actual value is `25`, but it's `Boolean` value is truthy. Given that `25` is a truthy value, what does a falsy value look like? Fantastic question! Below is a list of possible falsy values:

- `false`
- `0` // the number zero
- `""` // an empty string
- `undefined` // keyword meaning no defined value
- `NaN` // Not a Number, this is a global value that we will cover later
- `null` // keyword meaning void of value

::: tip
WAT?!?! False is a falsy value, that's right! You heard it here first folks! The important part about falsy values is that they resolve to a false value. In the case of the false value, it resolves to false therefore it is a falsy value.
:::

::: tip
A great exercise would be to take the falsy values above and make a `Test Case` with `Test Assertions`, that validate that each of them is indeed falsy. Here is the first `Test Assertion` to get you started:

```javascript
expect(false).toBeFalsy();
```
:::

Given the above are the falsy values of JavaScript, it stands to reason all other values are truthy. Listed below are a few examples of truthy values:

- `true`
- `25`
- `"One"`
- `[1, "two", 3]`
- `{ "name": "Software Wright" }`

::: tip
Again I would recommend writing a `Test Case` with `Test Assertions` validating that the following values are truthy. Below is the first `Test Assertion` to get you started:

```javascript
expect(true).toBeTruthy();
```
:::

:::tip
If you would like more details about truthy and falsy values there are links below:
- [Falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)
- [Truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)
:::

With all of this understood let's bring back our example

``` javascript {2}
var x = 25;
10 > 3 && x
```

In this case, since `x` is equal to `25` this means that it is a truthy value and the `Boolean Expression` to the right `10 > 3` is also true since `10` is greater than `3`. As a result, the entire statement will resolve to a `truthy` value, but enough talk let's prove it!

We'll add another `Test Assertion` to our `AND` operator `Test Case` above as well as the `x` variable.

``` javascript {4,7}
// test case description goes here
test("the AND operator checks that a combination of boolean expressions or variables resolve to a truthy value", () => {
 // assertions go here
 var x = 25;

 expect(3 < 10 && 3 > 2).toBeTrue();
 expect(10 > 3 && x).toBeTruthy();
})
```

Wait one second! What is this `toBeTruthy` thing? There is a specific reason that the description for the `AND` operator attempts to return a truthy value and not necessarily a true value. 

### Short Circuit Operators

Both the `AND` and `OR` operator are `Short Circuit Operators`, meaning that as they are evaluated from left to right if they can stop early they will. In the case of the `AND` operator if there is a false condition at any point it will stop executing and return that value. For example with the condition below:

``` javascript
0 && 3 < 10
```

Since the number `0` is a falsy value, the `AND` statement will immediately stop and will not bother checking the `Boolean Expression` `3 < 10`. Once the `AND` statement receives a single falsy value the entire statement is now false/falsy and it doesn't need to bother executing the rest. The important thing of note is that the value returned from the statement above is not false, but rather the falsy value that caused the statement to stop, meaning `0`. 

::: tip
I realize this sounds crazy, but that's ok JavaScript is crazy sometimes. I would recommend that you place the above example into a `Test Assertion` validating the result `toBe(0)`, or even better `toBeFalsy`. I would also recommend swapping out the `0` falsy value with some of the others like `undefined` or `""` proving that the statement still `toBeFalsy`. 
:::

Now if both of the statements in the `AND` operator are truthy then what will it return? The value returned from the `AND` operator will be the furthest right statement, so let's take the example below:

``` javascript
3 < 10 && 25
```

The first value of the statement above resolves to `true`, due to `3` being less than `10`. Though when the statement `25` is checked all the `AND` operator does is check that it is a `truthy` value since `25` is truthy the value will be returned. Don't take my word for it let's make a `Test Case` that proves this is how the `AND` operator works.

Let's start with a description of, `the AND operator returns the last truthy value if it does not short circuit`.

``` javascript 
// test case description goes here
test("the AND operator returns the last truthy value if it does not short circuit", () => {
 // assertions go here
})
```

Then we will store the result of the expression above into a variable so that we can add multiple `Test Assertions` for it.

``` javascript {4} 
// test case description goes here
test("the AND operator returns the last truthy value if it does not short circuit", () => {
 // assertions go here
 var value = 3 < 10 && 25;
})
```

Finally, we will perform our expectations, base on what we discussed we `expect` the returned value `toEqual(25)` and `toBeTruthy`.

``` javascript {4} 
// test case description goes here
test("the AND operator returns the last truthy value if it does not short circuit", () => {
 // assertions go here
 var value = 3 < 10 && 25;

 expect(value).toEqual(25);
 expect(value).toBeTruthy();
})
```

ANNNND there's your full proof! The `AND` operator returned the value `25` which we stored into a variable, then we asserted that `value` variable has two `values` it both the number `25` and a truthy value.

Give your `Test Cases` a run using `npm test` before moving on.

That's all for this tutorial on the `AND` operator, I would highly recommend working through the different challenges presented throughout the article to thoroughly solidify the concepts. Also, I have also left out that you can chain the `AND` operator as much as you like e.g. `3 > 10 && x && 10 === 10` so I would also recommend experimenting with that as well. Great job working through the tutorial and `keep testing on`!

If you have any questions or feedback feel free to [contact me](/contact) or leave a comment below, and always remember **you are the Captain of this Quality Cruise Line**.

## **Support this content through [Patreon](https://www.patreon.com/softwarewright)**

<EmailSubscription />

<vue-disqus shortname="softwarewright" identifier="logical_opeartors" :url="$page.url" />