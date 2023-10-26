# Object-Oriented Programming in JavaScript

You are not the only one to feel that JavaScript is weird. This is very common for people coming from other backgrounds. That's because JavaScript is not like those traditional class based languages.

## History

It is important to know the historical context around JavaScript's creation. In 1994 Netscape relased their web browser called Netscape navigator. The web browser was a major success. Brendan Eich convinced his boss at Netscape that the Navigator browser should have its own scripting language. At that time Java was very very popular. Netscape was negotiating with Sun to include it in Navigator. The big debate inside Netscape therefore became “why two languages? why not just Java?”.

They eventually decided to come up with a new language for their browser. But the diktat from upper engineering management was that the language must look like Java beceause they wanted to attract Java programmers. At the same time, they didn't want to compete with Java. So Brendan Eich did something different for JavaScript. He chose first-class functions from [Scheme](<https://en.wikipedia.org/wiki/Scheme_(programming_language)>) and prototypes from [Self](<https://en.wikipedia.org/wiki/Self_(programming_language)>) as the main ingredients.

## What is an Object?

In order to understand OOP in JavaScript, it’s important to understand the concept of objects. They are a primary part of JavaScript. Almost everything in JavaScript is an object.

> An object in JavaScript is simply a key-value pair where key is a string and the value can be anything.

It's simillar to a data structure called hash table. However, the implementation can vary on different JavaScript engines. The term Object can be confusing as we often associate it with classes in other language. In traditional OOP languages, you can't create objects without classes. In JavaScript, objects can be created on the fly without the need of classes.

### Creating objects in JavaScript

Just like we can create variables, we can create objects on the fly. An object  can be created in serveral ways. In the examples, we'll try to model an Object for a person. We'll have the following properties and methods in the object.

- `firstName` - first name of the person
- `lastName` - last name of the person
- `age` - age of the person
- `greet()` - method to greet a person
- `getFullName()` - method to get full name of the person
- `isAdult()` - method to to check if the person is a legal adult

#### Object Literals

Let's start with object literals. With the object literals syntax, you can create objects just with a set of curley braces - {}.

```js
const person = {
    firstName: "Naimul",
    lastName: "Haque",
    age: 26,
    getFullName: function () {
        return `${this.firstName} ${this.lastName}`;
    },
    isAdult: function() {
        return this.age >= 18;
    },
    greet: function() {
        console.log(`Hello, my name is ${this.getFullName()}`);
    }
};
```

#### Using `new Object()`

In JavaScript, the `new Object()` statement is used to create a new instance of the Object constructor. This essentially creates an empty object. It's equivalent to creating an empty object using object literal notation {}.

```js
const person = new Object(); 

person.firstName = "Naimul";
person.lastName = "Haque";
person.age = 26;
person.getFullName = function () {
    return `${this.firstName} ${this.lastName}`;
};
person.isAdult = function() {
    return this.age >= 18;
}
person.greet = function() {
    console.log(`Hello, my name is ${this.getFullName()}`);
}
```

#### Using `Object.create()`

The previous 2 methods of creating objects were somewhat regular. `Object.create()` creates a new object and returns the empty object, additionally it does one very important thing. We'll come back to it shortly.

```js
// ignore the null value we're passing into Object.create()
// we'll explain this shortly but anyway this creates an empty object
const person = Object.create(null);

person.firstName = "Naimul";
person.lastName = "Haque";
person.age = 26;
person.getFullName = function () {
    return `${this.firstName} ${this.lastName}`;
};
person.isAdult = function() {
    return this.age >= 18;
}
person.greet = function() {
    console.log(`Hello, my name is ${this.getFullName()}`);
}
```

### Issues in the previous 3 approaches

Let's first take a look at the issues that we have with the above 3 approaches we've described so far. Think about it, if we want to define more person, we have to repeat the same codes which is not a good practice. 

So what can we do here? Simple, we can create a reusable function, that will create and return these person objects for us.

```js
function createPerson(firstName, lastName, age) {
    const person = {};
    person.firstName = firstName;
    person.lastName = lastName;
    person.age = age;
    person.getFullName = function () {
        return `${this.firstName} ${this.lastName}`;
    };
    person.isAdult = function() {
        return this.age >= 18;
    }
    person.greet = function() {
        console.log(`Hello, my name is ${this.getFullName()}`);
    }
    return user;
}
const person1 = createPerson("Naimul", "Haque", 26);
const person2 = createPerson("John", "Doe", 32);
```

We can create many users with this function. `createPerson()` function is now reusable but it's extremely inefficient. The reason is simple, the common methods `getFullName()`, `isAdult()` and `greet()` are created on the person object everytime we create a new person. These methods are common functionality and doesn't need to have it's own copy for each object. We need some way to somehow share the common functionality.

### Sharing common functionalities

```js
function createPerson(firstName, lastName, age) {
    const person = {};
    person.firstName = firstName;
    person.lastName = lastName;
    person.age = age;
    return person;
}

// this is the object consisting of the methods that we want
// to share with all the persons created by the createPerson()
// function without creating a copy for each individual object
const commonMethods = {
    getFullName() {
        return `${this.firstName} ${this.lastName}`;
    },
    isAdult() {
        return this.age >= 18;
    }
    greet() {
        console.log(`Hello, my name is ${this.getFullName()}`);
    }
};
```

We've moved all the methods to another object. It's obvious that the `person` object doesn't know where to find the common methods. So, we need to somehow say to the person object that whenever you need a method, take a look at the `commonMethods` objects. The question is how do we do that?

#### 2.2.1 Introducing `__proto__`

Every object in JavaScript gets a special propertly called `__proto__`. Suppose we want to store `user.isMarried` in a variable. The property doesn't exist on the person object. So instead of giving up, JavaScript will try to find it in `__proto__`. So anything if JavaScript is unable to find some property or method in the actual object, it will look into the special property called `__proto__`. 

So that basically means if we can set `__proto__` of each `person` object to be the `commonMethods` object, our job is done. How do we do that? Remember `Object.create()`? It returns a new object, but the additional thing it does is, it sets the `__proto__` of that object to be the object that we pass into this as argument.

```js
function createPerson(firstName, lastName, age) {
    // it creates a new object called person and sets
    // the person object's __proto__ to be the commonMethods
    const person = Object.create(commonMethods);

    person.firstName = firstName;
    person.lastName = lastName;
    person.age = age;

    return person;
}

const commonMethods = {
    getFullName() {
        return `${this.firstName} ${this.lastName}`;
    },
    isAdult() {
        return this.age >= 18;
    }
    greet() {
        console.log(`Hello, my name is ${this.getFullName()}`);
    }
};
```

You might be thinking, this is simillar to how a `class` creates an object in other programming languages. Don't we have the `class` keyword in JavaScript? Yes, we have the `class` keyword, and we'll get there eventually. The reason I'm showing you all these in details is because, `class` is just a syntantic sugar in JavaScript. Even if you write classes, it's cruitial to understand. the underlying concepts.

Now, this solution definitly works, but its a bit declarative. We are handcrafting everything like creating objects with a different `__proto__`. Now JavaScript gives us a way to automate these stuffs with the `new` keyword.

### Functions are Objects

Before we learn about the `new` keyword, we need to understand that functions are objects behind the scene. We can attach properties to functions. The following code looks a bit strange but it is indeed valid in JavaScript.

```js
function sum(a, b) {
  return a + b;
}

sum.myName = "Naimul Haque";
console.log(sum.myName);
```

### Resources

[Performance of key lookup in JavaScript](https://stackoverflow.com/questions/7700987/performance-of-key-lookup-in-javascript-object)
