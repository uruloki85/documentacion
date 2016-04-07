#Linux
Informació d'interès.

##Copy via SSH
```
scp your_username@remotehost.edu:foobar.txt /some/local/directory
```

##Run a process already started in background
If you run a process and later you realize that it will last very long, you can send it to execute in background although it's already running:
###Way 1
* Ctrl + Z: sends the process to background but stopped
* <code>fg</code>: the process continues execution
* <code>disown</code>: the process won't be killed although you close the session

###Way 2
* Ctrl + AD: dettaches the process using <code>screen</code> (linux command). It keeps running in background.
* <code>screen -r</code>: to recover it.


##curl
Per enviar requests:
```
curl -X PUT --cookie "ega.services.session.id=1" -d level=INFO localhost:9400/logs
```
Opció:
- **-X**: per indicar el tipus de mètode.
- **-d**: per enviar dades POST. Cada atribut haurà d'anar precedit pel flag.
- **--data-urlencode**: per poder enviar caràcters especials. P.e. 
```
curl -X POST localhost:9200/sessions/ -d username=john --data-urlencode password="Cr&364"
```
- **-b/--cookie**: per indicar una cookie.
- **-H**: per afegir headers. P. e. 
```
curl -H "Authorization: 3" localhost:8086/submitter/v1/notifications
```
- **-i**: mostra les capceleres de la resposta
- **-v**: mode verbose (info. tant de la request com de la response)

###Send a file
```
curl -X POST -F data=@/path/to/file/name.txt localhost:8086/submitter/v1/submissions/{id}/runs/sequencing/csv
```
- **-F**: to emulate a form.
- **@**: prefix the file with **@** to attach it in the post as a file upload.

###Performance
```
curl -w %{time_starttransfer}\\n%{time_total}\\n -o /dev/null -s -X GET "localhost:9700/archiveservice/v1/users/123/files?sourceType=EBI_INBOX&limit=0"
```

##netstat
Proporciona informació dels **ports ocupats**.

Amb l'opció <code>-tapen</code> donarà informació sobre el procés (pid i nom) concret que està utilitzant cada port.

Així, si volem saber quina app està utilitzant un port determinat, hem d'executar:
```
sudo netstat -tapen | grep ":9400"
```

##JSON pretty print
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
##Create a Desktop icon
Open a terminal and type:
```
gnome-desktop-item-edit --create-new ~/Desktop
```

##Create a link to an app
Open a terminal and type:
```
cd /usr/local/bin
sudo ln -s ../squirrel-sql-3.2.1/squirrel-sql.sh squirrel-sql
```
After that, you can run the program typing <code>squirrel-sql</code>

