# Object getters and setters

Reading over Kyle Simpson's You Don't Know JavaScript, this and Object prototypes book there is an aspect about objects that I didn't know about, and this is they support a getter and setter interface.

Under the covers, when you reference an object property using dot notation, it's effectively looking (getting) the value. The default operation for retrieving a value can be overridden by defining your own within the object's specification. Suppose the property name you want in your object is `age`, inside your object, you would define the get function as so:

```js
get age(){
    return this._age;
}
```

As you expect, the set is much the same, just replacing `get` with `set`:

```js
set age(newAge){
    this._age = newAge;
}
```

Producing an overall object specification that looks like:

```js
var obj = {
    get age(){
        return this._age_;
    },

    set age(newAge){
        this._age_ = newAge;
    }
};
```

With that then, you would access the age property as you would typically expect.

```js
obj.age = 5;
console.log(obj.age);//5
```

Each property on an object has attributes under the covers. They include:

* value
* writable
* enumerable
* configurable

On any given property, you can find out the values of these with:

```js
Object.getOwnPropertyDescriptor(obj, "age")
```

To modify the default values for these attributes (default: true), we can use the function `Object.defineProperty`:

```js
Object.defineProperty(obj, "name", {
    value: "tschf",
    writable: false,
    enumerable: true,
    configurable: true
});
```

Similarly, to define a property with custom getter and setter, you just specify a `get` and `set` function in the object attributes like so:


```js
Object.defineProperty(obj, "gender", {
    get: function(){
        return this._gender;
    },

    set: function(newGender){
        this._gender = newGender;
    },

    enumerable: true
});
```
