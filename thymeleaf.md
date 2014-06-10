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

Preprocesar expresiones
-----------------------
En el siguiente ejemplo primero se evalúa la expresión entre __ (la innerExpression) y luego la expresión englobante (la outerExpression).
<pre><code>#{outerExpression.__*{innerExpression}__}</code></pre>

Formatear decimales
-------------------
Indica el número mínimo de dígitos enteros (1) y el número exacto de dígitos decimales (2), así como el separador de miles (punto) y decimales (coma).
<pre><code>${#numbers.formatDecimal(numToFormat,1,'POINT',2,'COMMA')}</code></pre>

Formatear fechas
----------------
Indica el formato de la fecha.
<pre><code>*{#dates.format(dateToFormat, 'dd/MM/yyyy')}</code></pre>

Paso de parámetros hacia Javascript
-----------------------------------
Se pueden crear todos los atributos que se quiera utilizando <code>data-th-attr</code> y separados por comas. 

Con ello conseguimos que se caclule el valor en tiempo de ejecución (si escribimos directamente <code>data-id-factura=${facturaConfrontador.idFactura}</code> como haríamos en una JSP, no se interpretará como una instrucción de Thymeleaf y en tiempo de ejecución la variable <code>data-id-factura</code> contendría el texto <code>${facturaConfrontador.idFactura}</code> en lugar de el valor contenido):
```html
<button type="button" class="btn btn-primary js-valida" data-th-text="#{boto.acceptar}" 
				data-th-if="${factura.validada eq 'P'}"
				data-th-attr="data-id-factura=${facturaConfrontador.idFactura}, data-url=@{/visor/valida}, data-acceptar=true">Aceptar</button>
```
* <code>data-th-if</code>: Sólo se mostrará el elemento html si se cumple la condición.

Estructura en un proyecto Spring Boot
=====================================
* <code>src/main/resources</code>: <code>application.properties</code>, <code>messages.properties</code>
El proyecto puede tener un solo fichero <code>messages.properties</code> y/o bien puede haber uno por HTML. En cualquier caso, si se quiere hacer uso de la internalización el nombre del fichero deberá ser <code>messages<b>_es</b>.properties</code>, <code>messages<b>_ca</b>.properties</code>, etc.
* <code>src/main/resources/<b>templates</b></code>: *.html.
* <code>src/main/resources/<b>static</b></code>: *.js, *.css, *.png, etc. Pueden crearse subcarpetas.

Añadir las siguientes líneas en <code>application.properties</code> para que servidor embebido se entere de los cambios en "caliente" (así no hay que redesplegar):
```
# Allow Thymeleaf templates to be reloaded at dev time
spring.thymeleaf.cache: false
server.tomcat.access_log_enabled: true
server.tomcat.basedir: target/tomcat
```
