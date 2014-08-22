#Linux
Informació d'interès.

###curl
Per enviar requests:
```
curl -X PUT --cookie="ega.services.session.id=1" -d level=INFO localhost:9400/notificationservice/v1/logs
```
Opció:
- **-X**: per indicar el tipus de mètode.
- **-d**: per enviar dades POST. Cada atribut haurà d'anar precedit pel flag.
- **-b/--cookie**: per indicar una cookie.
- **-H**: per afegir headers. P. e. 
```
curl -H "Authorization: 3" localhost:8086/submitter/v1/notifications
```
- **-i**: mostra les capceleres de la resposta
- **-v**: mode verbose (info. tant de la request com de la response)


###netstat
Proporciona informació dels **ports ocupats**.

Amb l'opció <code>-tapen</code> donarà informació sobre el procés (pid i nom) concret que està utilitzant cada port.

Així, si volem saber quina app està utilitzant un port determinat, hem d'executar:
```
sudo netstat -tapen | grep ":9400"
```

###JSON pretty print
Instal·lar la llibreria *yajl* que inclou les eines *json_reformat* i *json_verify*:
```
echo '{"b":2, "a":1}' | json_reformat
```
Imprimirà el següent:
```json
{
    "b": 2,
    "a": 1
}
```

###Create a Desktop icon
Open a terminal and type:
```
gnome-desktop-item-edit --create-new ~/Desktop
```

###Create a link to an app
Open a terminal and type:
```
cd /usr/local/bin
sudo ln -s ../squirrel-sql-3.2.1/squirrel-sql.sh squirrel-sql
```
After that, you can run the program typing <code>squirrel-sql</code>

