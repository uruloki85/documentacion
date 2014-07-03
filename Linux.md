#Linux
Informació d'interès.

###Ports utilitzats
```
sudo netstat -tapen
```
L'opció <code>-tapen</code> proporciona informació sobre el procés que està utilitzant el port.

Així, si volem saber quina app està utilitzant un port determinat, hem d'executar:
```
sudo netstat -tapen | grep ":9400 "
```
