# JavaScript and the prototype

All objects in JavaScript are linked to a prototype object. This object can contain attributes. There is a prototypal hierarchy/chain depending on the object, and when trying to access an attribute of a variable, if it's not directly in the attribute, it starts searching up the prototypal chain.

As a basic example, the `Function` object which obviously represents a function, we can add a method to its prototype:

```js
Function.prototype.age = 1;
Function.prototype.printObject = function(){
    console.log(this);
}

var foo = function(){
    console.log('Foo called');
};

foo();//Foo called
console.log(foo.age);1
foo.printObject();//function(){console.log('Foo called');}
```

The same applies to an object. It's important to note this, as the loop construct `for (key in object)` will loop over all accessible attributes, including those all the way up the prototype chain.

So, you will likely want to exclude properties up the prototype chain. This can be done with `object.hasOwnKey(keyName)`

```js
Object.prototype.bar = 'Barrr';
var keys = { "a" : 65, "b" : 66 };
for (key in keys){
    console.log('key: ' + key);//key: a; key: b; key: c;
}
for (key in keys){
    if (keys.hasOwnProperty(key)){
        console.log('key: ' + key);//key: a; key: b
    }
}
```

You can use this technique create a method on an Object. If you assign to the generic `Object` then it will apply to all objects. To make it more targeted, you can do like:

```js
var Person = function(){
    this.name = "NONAME";
    //this presumed returned
};

Person.prototype.getName = function(){
    return this.name;
};

var p = new Person();
console.log(p.getName());//NONAME

//could also be done as, but we have no typical structure
//ready for re-use without manually re-created the same
//structure
var p = {
    name: 'NONAME',

    getName: function(){
        return this.name;
    }
};

```

JavaScript uses prototype inheritance rather than class inheritance, so we need to use (and understand) the prototype system to effectively create a type hierarchy. There are a few ways in which a type hierarchy can be constructed.

The constructor pattern

When a function is made, there is a prototype property - this properties value is an object containing one attribute `constructor` which refers to the function itself.

```js
var P = function(){ console.log('p'); };
console.log(P.prototype.constructor);//function(){ console.log('p'); }
```

As we know, a constructor is what is run when creating a new instance of an object. So in order to set up prototypal inheritance, the `prototype` property of the function is where the inheritance logic would go. That is, we set the protype of any descending classes to a new instance of the parent class with `new Type`.

```js
//create the definition for our parent type
var Employee = function(name, location){
    this.name = name;
    this.location = location;
};

Employee.prototype.setLocation = function(newLoc){
    this.location = newLoc;
};

Employee.prototype.employeeInfo = function(){
    console.log(this.name + ' works at ' + this.location);
};

var bob = new Employee('Bob', 'Sydney');
console.log(bob.employeeInfo());

//now create a sub-type of Employee
var Engineer = function(name, location, engoField){
    this.name = name;
    this.location = location;
    this.engineeringField = engoField;//civil, mechanical, etc
};

Engineer.prototype = new Employee();

var tom = new Engineer('Tom', 'Brisbane', 'Civil');
console.log(tom.employeeInfo());//Tom works at Brisbane

```

In this example, the two hierarchies of Employee and Engineer look like:

.
└── Object
    └── Employee
        └── Engineer

So that when we construct Employee, in the prototype chain, it points directly to object. By adding the prototype constructor to Employee on the Engineer type, when creating it it points to an Employee instance. Without extending the employeeInfo function we still have that function available. As a brief extension to the above code block to demonstrate extending the `employeeInfo` function:

```js
Engineer.prototype.employeeInfo = function(){
    console.log(this.name + ' works at ' + this.location + ' as a ' + this.engineeringField + ' engineer.');
};

console.log(tom.employeeInfo());//Tom works at Brisbane as a Civil engineer.
```

Looking at the code, it isn't quite obvious what the line `Engineer.prototype = new Employee();` is about. And being that functions are first class citizens in JavaScript, we can improve this by creating a method with a nice name that describes what we are doing (I'll go with `descends`).

```js
Function.prototype.descends = function(parentType){
    this.prototype = new parentType();
}

//create the definition for our parent type
var Employee = function(name, location){
    this.name = name;
    this.location = location;
};

Employee.prototype.setLocation = function(newLoc){
    this.location = newLoc;
};

Employee.prototype.employeeInfo = function(){
    console.log(this.name + ' works at ' + this.location);
};

var bob = new Employee('Bob', 'Sydney');
console.log(bob.employeeInfo());

//now create a sub-type of Employee
var Engineer = function(name, location, engoField){
    this.name = name;
    this.location = location;
    this.engineeringField = engoField;//civil, mechanical, etc
};

Engineer.prototype.employeeInfo = function(){
    console.log(this.name + ' works at ' + this.location + ' as a ' + this.engineeringField + ' engineer.');
};

Engineer.descends(Employee);

var tom = new Engineer('Tom', 'Brisbane', 'Civil');
console.log(tom.employeeInfo());//Tom works at Brisbane
```

You need to be cautious of this pattern due to the requirement of getting an object with the `new` option. Some in the wider community discourage the use of `new` - I suggest having a read of this post on SO: http://stackoverflow.com/questions/383402/is-javascripts-new-keyword-considered-harmful. In particular, forgetting the new results on fields being added to the global namespace.

You shouldn't get too far in your program though, since your variable will just be an undefined object. You should do some more research if this style is for you or not.

```js
var peter = Engineer('Peter', 'Brisbane', 'Civil');
console.log(typeof peter);//undefined
console.log(window.engineeringField);//Civil
```

Object.create

We can avoid all this specifying the prototype parent by making use of the `Object.create` function. We basically create our base type, then extend from it by calling `Object.create` passing in the base type.

```js
var empBob = {
    name: 'Bob',
    location: 'Sydney',

    employeeInfo: function(){
        console.log(this.name + ' works at ' + this.location);
    }
};

var empTom = Object.create(empBob);
empTom.name = 'Tom';
empTom.location = 'Sydney';
empTom.engineeringField = 'Civil';

empTom.employeeInfo = function(){
    console.log(this.name + ' works at ' + this.location + ' as a ' + this.engineeringField + ' engineer.');
};
```

Here, empTom's prototype chain looks at the empBob object. Until we offer an alternative employeeInfo method, the same function will be used.

Closure objects

Another approach is to return a closure object. This gives the added advantage of allowing for privacy of fields so that that can't just be modified without calling a function to force any business rules that may be needed.

First, we make your employee constructor, then in our engineer constructor, we get an employee object and add extensions to differentiate the enginner.

Locally:

```js
var employee = function(name, location){
    var empName = name;
    var empLocation = location;

    return {
        getName: function getName(){
            return empName;
        },

        setName: function setName(newName){
            empName = newName;
        },

        getLocation: function getName(){
            return empLocation;
        },

        setLocation: function setName(newLocation){
            empLocation = newLocation;
        }
    };
};

var engineer = function(name, location, field){
    var engineeringField = field;
    var emp = employee(name, location);

    //extend to add engineering specifics
    emp.getField = function(){
        return engineeringField;
    };

    emp.setField = function(field){
        engField = field;
    };

    return emp;
};

var emp1 = employee('John', 'Sydney');
var eng1 = engineer('Frank', 'Darwin', 'Mechanical');

console.log(emp1.getName());//John
console.log(eng1.getName() + ' is a ' +  eng1.getField() + ' Enginner');//Frank
```
This does not have the some prototypal chain as previous examples, but just extends from `Object`.
