#Linux
Informació d'interès.

###curl
Per enviar requests:
```
curl -X PUT --cookie="ega.services.session.id=1" -d level=INFO localhost:9400/notificationservice/v1/logs
```
Opció:
- **-X**: per indicar el tipus de mètode.
- **-d**: per enviar dades POST. Cada atribut haurà d'anar precedits pel flag.
- **--cookie**: per indicar una cookie.


###netstat
Proporciona informació dels **ports ocupats**.

Amb l'opció <code>-tapen</code> donarà informació sobre el procés (pid i nom) concret que està utilitzant cada port.

Així, si volem saber quina app està utilitzant un port determinat, hem d'executar:
```
sudo netstat -tapen | grep ":9400 "
```
