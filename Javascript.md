[Javascript](https://developer.mozilla.org/bm/docs/Web/JavaScript/Reference)
==========
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
function isVegetarian() {
}; 
function isLowSodium() {
}; 

export { specialty, isVegetarian };
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
