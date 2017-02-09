# Property binding
These 3 are equivalent:
```javasct
<p #p2 title="{{'Hello ' + name}}">{{p2.title}}</p>
<p #p3 [title]="'Hello ' + name">{{p3.title}}</p>
<p #p4 bind-title="'Hello ' + name">{{p4.title}}</p>
```
