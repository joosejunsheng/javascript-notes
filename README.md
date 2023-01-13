## "this" keyword

`this` is often used to refer to particular context (or we can call it object). It refers to the object which the function belongs to. <br />
If `this` exists in function declarations, if the function is not invoked in any object or it is not assigned to object, we can think of the function belongs to `window` object so `this` will return `window` object. 

`window` object will look something like this:
```mjs
Window {0: Window, 1: Window, 2: global, 3: global, window: Window, self: Window, document: document, name: '', location: Location, …}
```

`this` will return as empty object if it's declared outside function, which also means it doesn't have context. <br />

#### Function Declarations:
```mjs
function f1(){
    console.log(this); // returns `window` object
}
console.log(this); // returns {}
f1();
```

#### Function Expressions: <br />
Example 1:
```mjs
let f1 = {
    method1(){
        console.log(this);
    },
    method2(){
        console.log(this);
    }
}

f1.method1(); // (2) {method1: method1(), method2: method...}
```

Example 2:
```mjs
let dog = {
    name: "Doggo",
    age: 5,
    getName(){
        console.log("My name is : " + this.name);
    }
}

dog.getName(); // returns "My name is : Doggo"
```

Example 3: (`this` in `class`)
```mjs
class Dog {
    constructor(){
      this.name = "Doggo"
    }

    getName(){
        console.log(this); // returns Dog {name: "Doggo"} (returns the class instance)
        console.log("My name is : " + this.name);  // returns "My name is : Doggo"
    }
}

let dog = new Dog(); // `this` will point to dog (class instance)
dog.getName();
```

You need to be careful because you might lose your instance variable and `this` by directly accessing methods
```mjs
let dog = {
    name: "Doggo",
    age: 5,
    getName(){
        console.log("My name is : " + this.name);
    }
}

dog.getName(); // returns "My name is : Doggo"
let dogGetName = dog.getName;
dogGetName(); // returns "My name is : undefined"
```
In this case, you will need `.bind()` to bind the `this` back to the dogGetName method to preserve the `this`.
```mjs
let dog = {
    name: "Doggo",
    age: 5,
    getName(){
        console.log("My name is : " + this.name);
    }
}

dog.getName(); // returns "My name is : Doggo"
let dogGetName = dog.getName.bind(dog);
dogGetName(); // returns "My name is : Doggo"
```

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
<br />
Array is not primitive because Array is a special type of objects.

```mjs
let s1 = new String("i am a string");
let s2 = "i am a string";
```

s1 is an object created from String object, whereas s2 is a string literal (a primitive value)

```mjs
s1.toUpperCase(); // returns "I AM A STRING"
s2.toUpperCase(); // returns "I AM A STRING"
```

#### If so, how is it possible for s2 to use method like .toUpperCase()?<br />
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

When we initialize an object with the `new` keyword, the `new` keyword will generate an empty constructor, unless we write it again to override it.<br />
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

## Event Loop
## Garbage Collection
Everything created in the form of variables, objects, functions will be allocated in memory and will be freed once we don't need them. <br />
We don't really need to control the garbage collection because V8 Javascript engine will do the work for us but it's always good to know!

The reason we need garbage collection is to prevent a well-known problem called memory leak.<br /> When it happens, the program will start to incorrectly manage the memory allocation, applications will fail, devices stopped working because memory the device ran out of memory. So that's the reason we need to clean up unused memory and constantly free them.

The common concept of garbage collection is to keep those that are in-use, and free those that are not in-use. <br />

For example:
```mjs
// It simply means a dog variable is referencing an object in the heap memory
// {name : "Goofy"} is the object that exists in the heap and it's referenced so garbage collector will keep it.
let dog = {
    name: "Goofy"
}

// Once we set the reference to null, which means that the dog is not pointing to the object anymore
// And because of nothing is referencing the object now, it will be garbage collected once it's detected and it will be freed from the memory.
dog = null;
```


A very common algorithm that V8 Javascript engine uses is Mark and Sweep.

## Closure
## Misc
1) Why do we get undefined in the next line after we console.log() our value in the console?<br />
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
