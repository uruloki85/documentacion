#Linux
Informació d'interès.

###curl
Per enviar requests:
```
curl -X PUT -d level=INFO localhost:9400/notificationservice/v1/logs
```
Opció:
- -X: per indicar el tipus de request.
- -d: per indicar paràmetres. Cada paràmetre haurà d'anar precedit per *-d*.


###netstat
Proporciona informació dels **ports ocupats**.

Amb l'opció <code>-tapen</code> donarà informació sobre el procés (pid i nom) concret que està utilitzant cada port.

Així, si volem saber quina app està utilitzant un port determinat, hem d'executar:
```
sudo netstat -tapen | grep ":9400 "
```
