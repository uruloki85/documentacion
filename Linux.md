#Linux
Informació d'interès.

###netstat
Proporciona informació dels **ports ocupats**.

Amb l'opció <code>-tapen</code> donarà informació sobre el procés (pid i nom) concret que està utilitzant cada port.

Així, si volem saber quina app està utilitzant un port determinat, hem d'executar:
```
sudo netstat -tapen | grep ":9400 "
```
