Thymeleaf
=========

Para compatibilizar con HTML 5, se deben utilizar los tags de Thymeleaf de la siguiente manera: <code>data-th-text</code>

th:utext
--------
Para texto con formato. Por ejemplo:
<pre><code>home.welcome=Welcome to our <b>fantastic</b> grocery store!</code></pre>
Hay que usarlo así:
<pre><code>&lt;p th:utext="#{home.welcome}">Welcome to our grocery store!&lt;/p></code></pre>
Si usasemos <code>th:text</code> se printarían por pantalla los tags de html.
