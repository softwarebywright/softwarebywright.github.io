---
title: "Jest Unit Testing: Jest Mock Functions"
description: "Understanding jest.fn() for mocking"
image: /posts/javascript.png
date: Fri Nov 01 2019 21:55:05
---



# {{$page.title}}

<center>
  <strong>{{$page.readingTime.text}}</strong> 
</center>


**Required Software**

- [NodeJS](https://nodejs.org/en/)

Welcome back for another exciting trip through the world of `jest testing` :smiley:! In this article, we will be discussing one of my favorite topics within `jest` and that is mocks, more specifically we will be doing a deep dive into `jest.fn()`. Before we begin it's important to understand the purpose of mocking and why mocking is extremely useful in testing.

## What is a mock?

Mocks mimic the functionality of code, that must be why they're such a joke. Get it? Because they `mimic` code :sweat_smile:, nevermind I'll stick to being a developer :nerd_face:. 

In all seriousness mocking is a powerful tool in testing. Mocks give you the ability to prove that given your external dependencies such as APIs, database, etc are functioning correctly, your code will execute as expected. As a result, you will gain faster feedback through unit testing with mocked dependencies, and who doesn't like fast feedback?

::: tip
Though mocking allows you to fake the results of external dependencies it is generally a good idea to perform some form of end to end testing to prove that your systems are consistently functioning together.
:::

### Mocking The hard way

All of that sounds great, but code is king :crown: so let's see some examples. We'll start with a simple `Users Service` class that has a single function `getUsers` and a `dependency` of `requestService`.

``` javascript {4,8}
// users.service.js

class UsersService {
  constructor(requestService) {
    this.requestService = requestService;
  }

  async getUsers() {
    const response = await this.requestService.get('/users');

    return response.data;
  }
}

module.exports = UsersService;
```

As you can see this function makes an API call using the `requestService` dependency supplied through the constructor, then returns the body of the request stored in the `property` named `data`.

Now if we wanted to test this code as is without mocking, there is a bit of leg work that we would have to do:

- Supply a request library like `axios` as the `requestService`
- Ensure there is a base URL set on `axios` so that `/users` works
- Set up an API with a users endpoint for this class to call
- And so on ...

But wait :pause_button:! Through the power of mocking we control the results of our dependencies, meaning when the `requestService.get` is called we choose what is returned from the call. We control the narrative :robot:, sorry I've been watching a lot of Mr. Robot lately.

Let's test this by creating a javascript file and using our `UsersService`.

::: tip
Create a `users.service.js` in a directory of your choice with the code above placed inside, then create an `index.js` file with the code below next to it.
:::

``` javascript
// index.js

const UsersService = require("./users.service.js");

// the main function is just to be able to run our async code.
const main = async () => {
  const usersService = new UsersService();

  const users = await usersService.getUsers();
  console.log(users);
}

main();
```

If you run the code above using `node index.js` from your terminal you get an error mentioning `TypeError: Cannot read property 'get' of undefined`. This is because we did not supply the required `requestService` dependency for the `usersService`.

We could install a package like `axios` or `fetch`, but we are here to explore the wonderful world :earth_americas: of mocks. Thus rather than making real calls to an API, we will be creating our own mock and then explore using the `jest.fn()` supplied by jest to mock our `requestService`.

The `requestService` has an interface that is modeled closely after the `axios` library. You can tell by observing the usage in the `UsersService.getUsers` function.

```javascript {3,5}
...
  async getUsers() {
    const response = await this.requestService.get('/users');

    return response.data;
  }
...
```

There are a few key points to notice about the `requestService` to mock the dependency correctly:

- It is an object that has a `get` function
- The `get` function takes a `URL` parameter that we use to make the request
- Since we `await` the `get` function call then the function must return the response in a promise
- The `getUsers` function expects the response object to have a `data` property

Keeping the above statements in mind we know that when creating our own mocked object we will need an `object` that contains a `get function` which takes a `URL parameter` and returns a `Promise` containing `response` object that has a `data` property. :sweat: Whew! That's a bit of logic to keep in mind, but with this knowledge let's create our `mock` `requestService` in the `index.js` file.


```javascript {3-8}
...
const main = async () => {
  const requestService = {
    get(url) {
      const response = { data: [] }
      return Promise.resolve(response)
    }
  };
  const usersService = new UsersService(requestService);
...
```

Great! If you run the tests again using the `npm test` command you should see the following in the terminal:

``` bash
$ node index.js
[]
```

Fantastic! We now have a stand-in mock for the `requestService`, but upon closer observation besides returning a static `response` the mock doesn't give us a lot of useful information.

As we are writing unit tests it's useful to understand more information about what has happened with the mock such as:

- If the mock function was called
- How many times the mock was called
- What was the mock function called with
- What happens if the mock returns different values
- What happens if the mock throws an exception

While our mock implementation is nice, it `jest` doesn't cut it when it comes to the functionality that we are looking for in a mock. Get it? `Jest` like the unit testing framework :sweat_smile:, alright last one I promise!

My point still stands if we are looking for a better `mock` then it's time we started to look at `jest's` mock functions.

## Jest Mock Functions

Jest's mock functions are a powerful tool at the heart of the testing framework. The mock functions give us a richer experience with mocking functionality such as:

- The number of calls made
- The parameters that are given for each call 
- Also, they allow different return values
- And so much more...

With this higher level of mocking, we can start to test our code more cohesively, but before we can start using jest mocks we have to set up the jest framework.

### Jest Setup

Let's begin by running the following commands:

``` bash
npm init -y
npm install --save-dev jest
```

::: tip
This will create a package.json file and install jest as a dev dependency.
:::

Next update the `test` `script` inside of the package.json file to the `jest` command.

``` json {7}
{
  "name": "mock-functions",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "jest"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "jest": "^24.9.0"
  }
}
```

And that's it! You are now ready to write to run jest tests, easy right :grinning:? Now let's get mocking!

### Mocking with jest.fn()

To start writing jest tests we need to create at least one `spec.js` file, so in your project create a `users.service.spec.js` file.

::: tip
The files can also end in `.test.js` and jest will automatically understand that is a test file that should be run. If you would like to use another file extensions check out the [test match docs](https://jestjs.io/docs/en/configuration#testmatch-array) for jest.
:::

Next, we are going to add our first test.

``` javascript
// users.service.spec.js

test("should request the users", () => {

})
```

Now that we have an empty test set up it's time that we fill it. We'll
start by creating a `usersService` instance to be tested.

``` javascript {3,7}
// users.service.spec.js

const UsersService = require("./users.service");

test("should request the users", () => {
  const usersService = new UsersService();
})
```

Similar to our custom mock object made before, we need to create `requestService` object and mock the `get` function. The difference this time is that we will be taking advantage of `jest's` mock functions to mock the `get` function of the dependency. To do this you simply assign the `get` function inside of the `requestService` object to `jest.fn()` and then pass the `requestService` dependency into the `UsersService` constructor which can be seen below.

``` javascript {7}
// users.service.spec.js

const UsersService = require("./users.service");

test("should request the users", () => {
  const requestService = { 
    get: jest.fn() 
  };
  const usersService = new UsersService(requestService);
})
```

This is a great start! We now that we have mocked the `get` function there is one additional step that we need to do, return a `Promise` that contains the response. Luckily the `jest` mock allows us to do this with a single function call, `mockResolvedValue` which will have the mock function you've created return a `Promise` containing the result of your choosing.

``` javascript {8}
// users.service.spec.js

const UsersService = require("./users.service");

test("should request the users", () => {
  const response = { data: [] };
  const requestService = { 
    get: jest.fn().mockResolvedValue(response)
  };
  const usersService = new UsersService(requestService);
})
```

Now that we've set up our mock function we are ready to test our `getUsers` function and make our assertions against the mock.

::: warning
Be sure to make the test function `async`. See the changes on line `5` of the code snippet below.
:::

``` javascript {5,12,14}
// users.service.spec.js

const UsersService = require("./users.service");

test("should request the users", async () => {
  const response = { data: [] };
  const requestService = { 
    get: jest.fn().mockResolvedValue(response)
  };
  const usersService = new UsersService(requestService);

  await usersService.getUsers();

  expect(requestService.get).toHaveBeenCalledWith("/users");
})
```

Nice! That's pretty awesome right, with very little effort we have created a mock object and proved that the `requestService` was called correctly when the `getUsers` function is called. 

Though if I'm being honest the code above feels a bit magical, so let's dive a bit deeper into inner workings the `jest.fn()`.


### jest.fn() deep dive

Let's step away from our current test for a bit to breakdown the `jest.fn()`. We'll start by creating a new test to be run and we'll skip our first test temporarily.

``` javascript
// users.service.spec.js

...
test.skip("should request the users", async () => {
...

test("jest.fn() deep dive", () => { })
```

Amazing! Now that we have our test case, let's talk mocks. To start off add a `variable` `mockFunction` to the test and assign it the value `jest.fn()`, then do a console log of the `variable` to see what you get back.

``` javascript
// users.service.spec.js

...
test("jest.fn() deep dive", () => {
    const mockFunction = jest.fn();

    console.log(mockFunction);
})
```

If you run your tests using the `npm test` command from your terminal, you should see the following output in the logs of your terminal. There are a lot of functions in this log of the `mockFunction` variable, but there is one that is very important to notice on line `5`. The `mock` property, this contains information about the calls that have been made to your `jest` `mock` function.

``` bash {5}
  console.log users.service.spec.js:18
    { [Function: mockConstructor]
      _isMockFunction: true,
      getMockImplementation: [Function],
      mock: [Getter/Setter],
      mockClear: [Function],
      mockReset: [Function],
      mockRestore: [Function],
      mockReturnValueOnce: [Function],
      mockResolvedValueOnce: [Function],
      mockRejectedValueOnce: [Function],
      mockReturnValue: [Function],
      mockResolvedValue: [Function],
      mockRejectedValue: [Function],
      mockImplementationOnce: [Function],
      mockImplementation: [Function],
      mockReturnThis: [Function],
      mockName: [Function],
      getMockName: [Function] }
```

To prove this let's change our console log to print out the `mock` property of the `mockFunction` variable instead.

``` javascript
test("jest.fn() deep dive", () => {
    const mockFunction = jest.fn();

    console.log(mockFunction.mock);
})
```

If you run the `npm test` command again we can see the output of the `mockFunction.mock` property.

``` bash
  console.log users.service.spec.js:18
    { calls: [], instances: [], invocationCallOrder: [], results: [] }
```

As you can see there have been no calls to the `mockFunction variable`, if we were to call the function and then console log again we can now see information about the calls that have been made to the `mockFunction` variable.

``` javascript
test("jest.fn() deep dive", () => {
    const mockFunction = jest.fn();

    console.log(mockFunction.mock);
    mockFunction("Hello Mocks");
    console.log(mockFunction.mock);
})
```

The first output will be the same as above, but with the second `console.log` we can see information about the call.

``` bash
  console.log users.service.spec.js:20
    { calls: [ [ 'Hello Mocks' ] ],
      instances: [ undefined ],
      invocationCallOrder: [ 1 ],
      results: [ { type: 'return', value: undefined } ] }
```

Notice that `calls` property has the parameters that the `mock` function was called with, the `instances` property specifies the `instances` of the mock function that you create with the `new` keyword, `invocationCallOrder` property tells you when the function was called, and finally `results` tells you what the function returned when it was called. 

::: tip
Since we did not specify that the function should return a value the `results` of the first call is undefined. Feel free to specify what the function returns using one of the following functions and see the value change.

- mockReturnValueOnce
- mockReturnValue
:::

Let's add some assertions to solidify what we learned. We'll start by writing the assertions in long-form, and then switch to the shorthand.

``` javascript {9,11,13}
test("jest.fn() deep dive", () => {
    const mockFunction = jest.fn();

    console.log(mockFunction.mock);
    mockFunction("Hello Mocks");
    console.log(mockFunction.mock);
    
    // expect the mock to have only been called once
    expect(mockFunction.mock.calls).toHaveLength(1)
    // expect the mock to have been called with the string "Hello Mocks"
    expect(mockFunction.mock.calls[0]).toEqual(["Hello Mocks"]);
    // expect the mock to have returned undefined
    expect(mockFunction.mock.results[0]).toEqual({ type: 'return', value: undefined });
})
```

Now let's see the shorthand

``` javascript {8-10}
test("jest.fn() deep dive", () => {
    const mockFunction = jest.fn();

    console.log(mockFunction.mock);
    mockFunction("Hello Mocks");
    console.log(mockFunction.mock);
    
    expect(mockFunction).toBeCalledTimes(1)
    expect(mockFunction).toHaveBeenCalledWith("Hello Mocks");
    expect(mockFunction).toHaveReturned(undefined);
})
```

Now that we have broken down the `jest.fn()`, I would highly recommend adding more mock function variables and calling them as well, changing out the return types, and watching as the mock object changes with each call.


That's it for this tutorial on `jest mock functions` great work making it through :clap:! If you have any questions or feedback feel free to [contact me](/contact) or leave a comment below, and always remember **you are the Captain of this Quality Cruise Line**.

Resources
- [Source Code](https://github.com/softwarewright/jest-mock-functions)
- [Jest Mock Functions](https://jestjs.io/docs/en/mock-functions)

### Share <social :url="$page.path" />

<hr/>

<comments shortname="softwarewright" identifier="jest-mock-functions" :url="$page.path" />
