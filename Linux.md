#Linux
Informació d'interès.

###Ports utilitzats: netstat
Proporciona informació dels ports ocupats.

L'opció <code>-tapen</code> proporciona informació sobre el procés (pid i nom) que està utilitzant el port.

Així, si volem saber quina app està utilitzant un port determinat, hem d'executar:
```
sudo netstat -tapen | grep ":9400 "
```
