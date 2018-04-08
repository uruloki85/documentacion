[Javascript](https://developer.mozilla.org/bm/docs/Web/JavaScript/Reference)
==========
# Functions
Functions in JavaScript are generally declared as either a function declaration or a function expression.
* Function declaration:
```
function square (number) {
  return number * number; 
}
console.log(square(5));
```
* Function expression:
The funciton identifier can be omitted, creating an anonymous function. 
```
const square = function (number) {
  return number * number;
};
console.log(square(5));
```
  * Arrow function:
```
const square = (number) => {
  return number * number;
};
```
  * Concise body:
    1. Functions that take a single parameter should not use parentheses. The code will still work, but it's better practice to omit the parentheses around single parameters. However, if a function takes zero or multiple parameters, parentheses are required.
    1. A function composed of a sole single-line block is automatically returned. The contents of the block should immediately follow the arrow => and the return keyword can be removed. This is referred to as implicit return.
    1. A function composed of a sole single-line block does not need brackets.
```
const square = number => number*number;
```
# Objects
```
let myObject = {
  name: 'Epi',
  age: 7
}
```
# Modules
```
let Airplane = {};
Airplane.myAirplane = 'StarJet';
```
* Exporting
```
module.exports = Airplane;
// or
export default Airplane;
```
or wrap any collection of data and functions in an object and exporting it:
```
let Airplane = {};

module.exports = {
  myAirplane: "StarJet",
  displayAirplane: function() {
    return this.myAirplane;
  }
};
```
Another way of exporting is using variables:
```
let specialty = '';
function isVegetarian() {}; 
function isLowSodium() {}; 

export { specialty, isVegetarian };
```
or
```
export let specialty = '';
export function isVegetarian() {}; 
```
* Importing 
To import this in another file:
```
const Airplane = require('./2-airplane.js');
// or
import Airplane from './airplane';

console.log(Airplane.displayAirplane());
```
Another way of importing is using variables:
```
import { specialty, isVegetarian } from './menu';

console.log(specialty);
```
