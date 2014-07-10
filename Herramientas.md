#Herramientas

###Añadir cookies en una request
Si utilizamos Chrome, abrimos la consola para desarroladores (Ctrl + Mayús. + i). En la pestaña Resources > Cookies podremos ver las cookies que existen en la página. Para añadir, vamos a la pestaña Console y ejecutamos el siguiente comando:
```javascript
document.cookie="nombre_de_la_cookie=valor_de_la_cookie"
```
Después, desde Spring podemos capturarla así:
```java
  @RequestMapping("")
  @ResponseBody
  public BasicDTO<NotificationModel> listNotifications(
      @CookieValue(value = "ega.services.session.id") Cookie sessionCookie, 
      @RequestParam(value = "userId", required = false) String userId) {

    return notificationService.listNotifications(sessionCookie, userId);
  }
```
Si Spring no encuentra la cookie en la request lanzará una excepción.
