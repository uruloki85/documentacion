# [Thymeleaf](http://www.thymeleaf.org/doc/html/Using-Thymeleaf.html)

Para compatibilizar con HTML 5, se deben utilizar los tags de Thymeleaf de la siguiente manera: <code>data-th-text</code>

## th:utext
Para texto con formato. Por ejemplo, en un fichero <code>properties</code>:
<pre><code>home.welcome=Welcome to our &lt;b>fantastic&lt;/b> grocery store!</code></pre>
Hay que usarlo así:
<pre><code>&lt;p th:utext="#{home.welcome}">Welcome to our grocery store!&lt;/p></code></pre>
Si usasemos <code>th:text</code> se printarían por pantalla los tags de html.

## Preprocesar expresiones
En el siguiente ejemplo primero se evalúa la expresión entre __ (la innerExpression) y luego la expresión englobante (la outerExpression).
<pre><code>#{outerExpression.__*{innerExpression}__}</code></pre>

## Formatear decimales
Indica el número mínimo de dígitos enteros (1) y el número exacto de dígitos decimales (2), así como el separador de miles (punto) y decimales (coma).
<pre><code>${#numbers.formatDecimal(numToFormat,1,'POINT',2,'COMMA')}</code></pre>

## Formatear fechas
Indica el formato de la fecha.
<pre><code>*{#dates.format(dateToFormat, 'dd/MM/yyyy')}</code></pre>

## Paso de parámetros hacia Javascript
Se pueden crear todos los atributos que se quiera utilizando <code>data-th-attr</code> y separados por comas. 

Con ello conseguiremos que se caclule el valor en tiempo de ejecución (si escribimos <code>data-id-factura=${facturaConfrontador.idFactura}</code> como haríamos en una JSP, no se interpretará como una instrucción de Thymeleaf y en tiempo de ejecución la variable <code>data-id-factura</code> contendrá el texto <code>${facturaConfrontador.idFactura}</code> en lugar del valor contenido):
```html
<button type="button" class="btn btn-primary js-valida" data-th-text="#{boto.acceptar}" 
				data-th-if="${factura.validada eq 'P'}"
				data-th-attr="data-id-factura=${facturaConfrontador.idFactura}, data-url=@{/visor/valida}, data-acceptar=true">Aceptar</button>
```

* <code>data-th-if</code>: Sólo se mostrará el elemento html si se cumple la condición.
 
Hecho esto, podremos recupear los valores desde Javascript:
```javascript
function validaFactura(event) {
	var contextBaseUrl = $(event.target).data('url');
	var idFactura = $(event.target).data('id-factura');
	var acceptar = $(event.target).data('acceptar');
	
	$.ajax({
		url: contextBaseUrl,
		cache: false,
		dataType: "json",
		type: "POST",
		data: {
			idFactura: idFactura,
	        acceptar: acceptar
		}
	}).done(function () {
		alert("done");
	}).fail(function(jqXHR, textStatus, errorThrown) {
		alert("fail: "+ textStatus + ", " + errorThrown);
	});
}
```

## Estructura en un proyecto Spring Boot
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

## Invocar métodos externos
Thymeleaf nos permite invocar métodos de <code>beans</code> definidos en el contexto de Spring. Para añadir uno nuevo:

* Crear la clase implementadora
```java
public class DateTimeFormatter {
	private static final String DATE_FORMAT = "dd/MM/yyyy";
	 
    public static String formatejaData(DateTime datetime) {
        return datetime.toString(DATE_FORMAT);
    }
}
```
* Crear el <code>bean</code> indicando que la clase implementadora es la anterior:
```java
@Component
public class DateTimeFormatterConfiguration {

	@Bean
	public DateTimeFormatter dateTimeFormatter() {
		return new DateTimeFormatter();
	}
}
```
* Hecho esto, podremos invocar los métodos de la clase desde los Html así:
```html
<td data-th-text="${beans.dateTimeFormatter.formatejaData(factura.dataValidacio)}">15/01/2014</td>
```
En este ejemplo hemos conseguido formatear una variable de tipo <code>DateTime</code> (Thymeleaf solo trae funciones propias para formatear los tipos <code>Date</code> y <code>Calendar</code>.

También es posible acceder a clases y métodos:
```html
<span data-th-text="T(es.upcnet.efactura.enums.Estat).PENDENT.codi}">P</span>
```
Donde <code>Estat</code> es una enumeración.

Más posiblidades de Thymeleaf en http://doanduyhai.wordpress.com/2012/04/14/spring-mvc-part-iv-thymeleaf-advanced-usage/

