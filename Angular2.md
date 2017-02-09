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
* <code>[ngModel]="name"</code> -> binding from the html to the component variable (whatever is written in the input is set to the variable "name")
* <code>(ngModel)="name"</code> -> binding from the component variable to the html (in the input the value of variable "name" is shown)
* <code>[(ngModel)]="name"</code> -> 2 way binding
* <code>bindon-ngModel="name"</code> -> canonical form of the 2 way binding

# Concurrent calls to back-end
```javascript
var serverCalls = Observable.forkJoin(
    // Get the user
    this.http.get(backendURL)
      .map((res: any) => JSON.parse(res._body))
      .catch(e => Observable.of({})),
    // Get the institution
    this.http.get(backendURL)
      .map((res: any) => JSON.parse(res._body))
      .catch(e => Observable.of({}))
  );

serverCalls.subscribe(
  data => {
    item.user = data[0];
    item.institution = data[1];
    ...
  },
  err => console.error('***ERROR***', err)
);
```
Notice the <code>catch</code> block. If a call returns an error an <code>Observable</code> is returned so the next calls can be executed.
