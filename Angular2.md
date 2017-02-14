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
Notice the <code>catch</code> block. If a call throws an error an <code>Observable</code> is returned and the following calls are executed (otherwise the flow is stopped).

# Concurrent calls which depend on an initial call
The first call returns an array. For each of the elements of the array, 4 concurrent calls are done and the array updated.
```javascript
public getAllUserInstitutionBox(limit?, skip?): Observable<any> {
  return this.formService.list(this._type, skip, limit, 'id')
    .map((response: any) => response._embedded.user_institution_box)
    .flatMap((user_institution_box: any[]) => {
      if (user_institution_box.length > 0) {
        return Observable.forkJoin(
          user_institution_box.map((item: any) => {
            return Observable.forkJoin(
              this.formService.listRaw(item._links.user.href).catch(e => Observable.of({})),
              this.formService.listRaw(item._links.institution.href).catch(e => Observable.of({})),
              this.formService.listRaw(item._links.box.href).catch(e => Observable.of({})),
              this.formService.listRaw(item._links.consortium.href).catch(e => Observable.of({}))
            ).map((data: any[]) => {
              item.user = data[0].username + ' [id: ' + data[0].id + ']';
              item.institution = data[1].institutionName + ' [id: ' + data[1].id + ']';
              item.box = data[2].box + ' [id: ' + data[2].id + ']';
              item.consortium = data[3].name + ' [id: ' + data[3].id + ']';
              return item;
            });
          })
        )
      }
    });
}
```
