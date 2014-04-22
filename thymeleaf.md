[Thymeleaf](http://www.thymeleaf.org/doc/html/Using-Thymeleaf.html)
=========

Para compatibilizar con HTML 5, se deben utilizar los tags de Thymeleaf de la siguiente manera: <code>data-th-text</code>

th:utext
--------
Para texto con formato. Por ejemplo, en un fichero <code>properties</code>:
<pre><code>home.welcome=Welcome to our &lt;b>fantastic&lt;/b> grocery store!</code></pre>
Hay que usarlo así:
<pre><code>&lt;p th:utext="#{home.welcome}">Welcome to our grocery store!&lt;/p></code></pre>
Si usasemos <code>th:text</code> se printarían por pantalla los tags de html.

Formatear decimales
-------------------
Indica el número mínimo de dígitos enteros (1) y el número exacto de dígitos decimales (2), así como el separador de miles (punto) y decimales (coma).
<pre><code>${#numbers.formatDecimal(num,1,'POINT',2,'COMMA')}</code></pre>
