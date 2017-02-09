# Property binding
These 3 are equivalent:
```html
<p #p2 title="{{'Hello ' + name}}">{{p2.title}}</p> <!-- Use of {{}} is called interpolation -->
<p #p3 [title]="'Hello ' + name">{{p3.title}}</p>
<p #p4 bind-title="'Hello ' + name">{{p4.title}}</p> <!-- bind-xxx is known as the canonical form and is the same as [xxx] -->
```
# Attribute Binding
If there is no equivalent property for the attribute we are trying to set (two such examples are colspan and aria-label) and we need to use a template expression to bind to this value, in this case we must use the attr directive:
```html
<table #t border=1 [attr.aria-label]="'Table with ' + t.rows.length + ' rows'">
```
# Event Binding with Parentheses
These 2 are equivalent:
```html
<div class="box" (mouseenter)="onEvent($event)"
                   on-click="onEvent($event)"> <-- on-xxx is know as the canonical form -->
</div>
```
# Two-way Binding
```html
<div>
    <label>Name:</label>
    <input type="text" [(ngModel)]="name">
    <h1 [hidden]="!name">Hello {{name}}!</h1>
</div>
```
* [ngModel]="name" -> binding from the html to the component variable (whatever is written in the input is set to the variable "name")
* (ngModel)="name" -> binding from the component variable to the html (in the input the value of variable "name" is shown)
* [(ngModel)]="name" -> 2 way binding
* bindon-ngModel="name" -> canonical form of the 2 way binding
