# `this`

`this` is a keyword available for use it all functions/method in JavaScript, but what it refers to depends on how a function has been called.

There are 4 basic cases what this can refer to:

1. method
* function
* constructor
* function.apply
* Event handler

## Method

A method is basically a function that is called from an object. An example of a method is the `push` function attached to the Array prototype. It could also be a function you have defined within your object.

It can be used to access variables within the scope of the variable.

```js
//attaching a method to a list
var numList = [1,5,2,7,87,33];
Array.prototype.printSize = function(){
    console.log(this.length);
};

var person = {
    name: "tschf",

    printName: function(){
        console.log(this.name);
    }
};

numList.printSize();//6
person.printName();//tschf
```

## Function

A function is any function declared not specific to any particular object. Since it doesn't belong to any object, it's in the global space and thus `this` is belonging to the global namespace.

```js
function setToTschf(){
    this.name = 'tschf';
}

setToTschf();
console.log(window.name);//tschf
```

Back to our object example, this is also present in any inner function we declare.

```js
var person = {
    name: "tschf",

    setMonth1: function(){
        function applyMonth(newMonth){
            this.month1 = newMonth;
        }
        applyMonth(11);

    },
    setMonth2: function(){
        this.month2 = 11;
    }
};

person.setMonth1();
person.setMonth2();

console.log(person.month1);//undefined
console.log(window.month1);//11
console.log(person.month2);//11
```

This work around of course is to pass in a reference to `this`. The convention seems to be setting `that` to `this`.

## Constructor

When an object is created with the `new` keyword, we also get a context of `this` specific to the object.

```js
var Person = function(name){
    this.name = name;

    this.getName = function(){
        return this.name;
    };
}

var p1 = new Person('tschf');

console.log(p1.getName());//tschf
```

Careful thought - leave of `new` when constructing the variable, and `this` is referring to the global namespace, as we learned earlier on.

## function.apply

Functions come with an `apply` method, so with a reference to your function, you can call `apply` passing in a context for what `this` should be applicable to.

```js
var foo = function(){
    console.log(this.bar);
};

foo();//undefined
window.bar = 'ZYX';
foo();//ZYX
foo.apply({bar: 'ABC'});//ABC
```

## Event handler

Then, on event handlers, this will refer to the dom node that it refers to.

```js
<h2 id="myHeading">Awesome heading</h2>

function headingClicked(){
    console.log(this);
}

var header = document.getElementById("myHeading");
header.onclick = headingClicked;

//clicked: <h2 id="myHeading">Awesome heading</h2>
```

If you want `this` to instead refer to something else, you can pass that in with the `bind` function.

```js
var header = document.getElementById("myHeading");
header.onclick = headingClicked.bind({"foo" : "bar"});

//clicked: Object {foo: "bar"}
```

Resources:

* JavaScript: The Good Parts, Douglas Crockford
* http://stackoverflow.com/questions/1369004/this-keyword-in-event-methods-when-using-javascript-prototype-object
