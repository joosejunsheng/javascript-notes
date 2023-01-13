## Prototype

We don't usually call valueOf() method because Javascript will invoke it automatically when a primitive value is expected from an object.

Primitive values are values which are:
1) Not object
2) No methods or properties

Primitive values are:
1) String
2) Number
3) BigInt
4) Boolean
5) Undefined
6) Symbol
7) Null

```mjs
let s1 = new String("i am a string");
let s2 = "i am a string";
```

s1 is an object created from String object, whereas s2 is a string literal (a primitive value)

```mjs
s1.toUpperCase(); // returns "I AM A STRING"
s2.toUpperCase(); // returns "I AM A STRING"
```

If so, how is it possible for s2 to use method like .toUpperCase()?
s2 will still has the access to all String object methods because Javascript will temporarily cast literals to String object in order to run the method.

Each object has a "private" property which holds a link to another object called its prototype. And .toUpperCase() is a method from String.prototype.

```mjs
let pet = {
    play(){
        console.log("I am playing!");
    }
}

let dog = {
    bark(){
        console.log("Woof woof!");
    }
}

// Dog inherits all the methods from pet.
dog.__proto__ = pet;

dog.play(); // returns "I am playing!"
dog.__proto__.play(); // returns "I am playing!"

Object.setPrototypeOf(dog, pet); // similar to dog.__proto__ = pet
Object.getPrototypeOf(dog).play(); // returns "I am playing"
```

Can also be written as
```mjs
let dog = {
    __proto__: pet,
    bark(){
        console.log("Woof woof!");
    }
}
```

When we initialize an object with the new keyword, they new keyword will generate an empty constructor, unless we write it again to override it.
Once the object instance is created, the prototype will point itself to the empty constructor.

Do take note that function expression doesn't have a constructor, only function declaration has.

```mjs
function Machine(){}
console.log(Machine.prototype); // this will return {constructor: f}

let Machine = {}
console.log(Machine.prototype); // this will return undefined

let coffeeMachine = new Machine();
console.log(coffeeMachine.__proto__); // this will return {constructor: f} (Machine / parent constructor)
```

We can also programatically set the prototype for our function / object

Example 1: 

```mjs
let electronics = {
    useElectricity: true,
}
Machine.prototype = electronics;
let coffeeMachine = new Machine();
console.log(coffeeMachine.__proto__); // this will return {useElectricity: true}
```

Example 2:
```mjs
const arr = [1, 2, 3];
// Can write a better version of .map() to override the default method if you want

// Array.prototype.map = () => {
// 	  Write your better .map() algorithm
// };

Array.prototype.customMethod = () => {
 	return "AA"; 
};

arr.customMethod(); // returns "AA"
```

## Misc
1) Why do we get undefined in the next line after we console.log() our value in the console.
The reason of this is that because console.log() does not return anything. Same goes to declaration of variables.

```mjs
> console.log("Yes");
Yes
undefined

> console.log(1 + 2 + 3);
6
undefined

> 1 + 2 + 3
6

> let test = 1
undefined
> test
1
```
