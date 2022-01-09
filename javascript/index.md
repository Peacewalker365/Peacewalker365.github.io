# JavaScript

#  JS

## Let/Const

```javascript

function sayHello() {
    for (var i = 0; i < 5; i++) {
        console.log(i);
      }

    console.log(i);
  }

sayHello();

```
problem with var: the scope!
To solve this problem, we have `let`.


#### Scope 
var -> function
let -> block
const -> block 



```javascript
const person = {
  name: 'Mosh',
  walk: function() {},
  talk() {} //neat way
};

person.talk();
person['name'] = 'John';
const targetMember = 'name'; // think about this can be user input
person[targetMember] = 'John'; // dynamically manage the access

```




## Objects

## this



```javascript
const person = {
  name: "Mosh",
  walk() {
    console.log(this);
  }
};

person.walk(); // {name: "Mosh", walk: f}

const walk = person.walk;
walk(); // undefined   since the browser runs it in the strict mode

```



What is 'this'?

'this' always return the current object.



The value of 'this' is determined by how the function is called.

If we call a function as a method of a object, 'this' returns the ref of the obj.

However, if we call a function as a standalone object or outside of an obj, 'this' will return the global obj -- the browser.



```javascript
const walk = person.walk.bind(person); // now the walk will always be binded to person obj for the new function walk created permanently.
walk(); 
```



### Building this

How to fix the prob: no matter how we call the function, 'this' always returns the person obj.

**notice that in JS, functions are objs**

So, `person.walk` is actually an obj.

`person.walk.bind(person);` will bind the it to the person obj, and 'this' will return the person obj.

## Arrow Funcs



```javascript
const square = function(number) {
  return number * number;
}

const square = number => {
  return number * number;
}

const square = number => number * number;

console.log(square(5));
```



```javascript
const jobs = {
  {id: 1, isActive: true},
  {id: 2, isActive: true},
  {id: 3, isActive: false}
};

const activeJobs = jobs.filter(function(job){ return job.isActive;});
const activeJobs = jobs.filter(job => job.isActive);
```



### Arow Funcs and this



## Destructuring

## Spread

## Classes

## Modules


