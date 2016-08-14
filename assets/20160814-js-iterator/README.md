# Iterators

The basic way to loop over an array is with a traditional for loop.

```js
var arr = [5,7,3,1];
for(var i = 0; i < arr.length; i++){
    console.log(arr[i]);
}//5, 7, 3, 1
```

Amd we can loop over properties in a JSON object with the `for in` construct.

```js
var obj = {a: 6, c: 4 };
for(var key in obj){
    console.log(obj[key]);
}//a, c
```

This `for in` construct offers a nice clean syntax. In newer version of JavaScript (ES6), the `for of` construct was introduced which can be used against arrays, which behaves much like `for in` but on arrays instead of objects.

```js
var arr = [5,7,3,1];
for(var i of arr){
    console.log(i);
}//5, 7, 3, 1
```

This is done by getting the iterator of the array and behind the scenes calling `.next()` until no more values exist. The iterator is retrieved by referencing `Symbols.iterator` in the arrays subscript. As per:

```js
var arr = [5,7,3,1];
var arrIt = arr[Symbol.iterator]();

var rw;
while((rw = arrIt.next()).done){
    console.log(rw);
}
```
