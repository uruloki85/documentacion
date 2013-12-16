jQuery
======

Siempre empezar con:
<pre><code>$(document).ready(function(){
   // jQuery methods go here...
}); </code></pre>
para asegurar que el código jQuery no se ejecutará antes de que se haya cargado la página.


[Selectores](http://www.w3schools.com/jquery/jquery_selectors.asp)
------------

* The element Selector
You can select all &lt;p> elements on a page like this:
<pre><code>$("p") </code></pre>

* The #id Selector
The jQuery #id selector uses the id attribute of an HTML tag to find the specific element.
<pre><code>$("#test")</code></pre>

* The .class Selector
The jQuery class selector finds elements with a specific class.
<pre><code>$(".test")</code></pre>

[All available jQuery selectors](http://www.w3schools.com/jquery/jquery_ref_selectors.asp)

Para utilizar funciones declaradas en otro fichero: 
<pre><code>&lt;script src="nombre_fichero.js">&lt;/script></pre></code>

[Event methods](http://www.w3schools.com/jquery/jquery_events.asp)
---------------
To assign a click event to all paragraphs on a page, you can do this:
<pre><code>$("p").click();</pre></code>

The next step is to define what should happen when the event fires. You must pass a function to the event:
<pre><code>$("p").click(function(){
  // action goes here!!
});</pre></code>

Most common events:
* click(): The function is executed when the user clicks on the HTML element.
* dblclick(): The function is executed when the user double-clicks on the HTML element.
* mouseenter(): The function is executed when the mouse pointer enters the HTML element.
* mouseleave(): The function is executed when the mouse pointer leaves the HTML element.
* mousedown(): The function is executed, when the left mouse button is pressed down, while the mouse is over the HTML element.
* mouseup(): The function is executed, when the left mouse button is released, while the mouse is over the HTML element.
* hover(): 
The hover() method takes two functions and is a combination of the mouseenter() and mouseleave() methods.
The first function is executed when the mouse enters the HTML element, and the second function is executed when the mouse leaves the HTML element
<pre><code>$("#p1").hover(function(){
  alert("You entered p1!");
  },
  function(){
  alert("Bye! You now leave p1!");
}); </pre></code>
* focus(): The function is executed when the form field gets focus.
* blur(): The function is executed when the form field loses focus.

[All available jQuery event methods](http://www.w3schools.com/jquery/jquery_ref_events.asp)

