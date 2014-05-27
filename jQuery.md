[jQuery](http://api.jquery.com/)
======

Siempre empezar con:
<pre><code>$(document).ready(function(){
   // jQuery methods go here...
}); </code></pre>
para asegurar que el código jQuery no se ejecutará antes de que se haya cargado la página.


[Selectors](http://www.w3schools.com/jquery/jquery_selectors.asp)
------------

* The element Selector<br/>
You can select all <code>&lt;p></code> elements on a page like this:
<pre><code>$("p") </code></pre>

* The #id Selector<br/>
The jQuery #id selector uses the <b>id</b> attribute of an HTML tag to find the specific element.
<pre><code>$("#test")</code></pre>

* The .class Selector<br/>
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
* <code>click()</code>: The function is executed when the user clicks on the HTML element.
* <code>dblclick()</code>: The function is executed when the user double-clicks on the HTML element.
* <code>mouseenter()</code>: The function is executed when the mouse pointer enters the HTML element.
* <code>mouseleave()</code>: The function is executed when the mouse pointer leaves the HTML element.
* <code>mousedown()</code>: The function is executed, when the left mouse button is pressed down, while the mouse is over the HTML element.
* <code>mouseup()</code>: The function is executed, when the left mouse button is released, while the mouse is over the HTML element.
* <code>hover()</code>: The <code>hover()</code> method takes two functions and is a combination of the <code>mouseenter()</code> and <code>mouseleave()</code> methods.
The first function is executed when the mouse enters the HTML element, and the second function is executed when the mouse leaves the HTML element
<pre><code>$("#p1").hover(function(){
  alert("You entered p1!");
  },
  function(){
  alert("Bye! You now leave p1!");
}); </pre></code>
* <code>focus()</code>: The function is executed when the form field gets focus.
* <code>blur()</code>: The function is executed when the form field loses focus.

[All available jQuery event methods](http://www.w3schools.com/jquery/jquery_ref_events.asp)

