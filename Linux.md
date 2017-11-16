# Table of contents
1. [Copy via SSH](#copy-via-ssh)
1. [Change permissions on files](#chmod)
1. [Find all files containing a string](#find-content-in-files)
1. [egrep](#egrep)
1. [Running a process in background](#run-process-in-background)
1. [curl](#curl)
    1. [Send a file](#curl-send-file)
    1. [Performance](#curl-performance)
1. [netstat](#netstat)
1. [Test performance](#test-performance)
    1. [USB](#performance-usb)
    1. [Network](#performance-network)
1. [JSON pretty print](#json-pretty-print)
1. [Create a link to an app](#link-to-app)
1. [Create a Desktop icon](#desktop-icon)

<a name="copy-via-ssh"></a>
# Copy via SSH
```
scp your_username@remotehost.edu:foobar.txt /some/local/directory
```
<a name="chmod"></a>
# Change permissions on files
```
chmod u=rwx,g=rx,o=r filename
```
* <code>u</code>: user
* <code>g</code>: group
* <code>o</code>: other

<a name="find-content-in-files"></a>
# Find all files containing a string
```
grep -rnw '/path/to/somewhere/' -e "pattern"
```
* <code>-r</code> or <code>-R</code> is recursive,
* <code>-n</code> is line number, and
* <code>-w</code> stands match the whole word.
* <code>-l</code> (lower-case L) can be added to just give the file name of matching files.

Along with these, <code>--exclude</code> or <code>--include</code> parameter could be used for efficient searching. Something like below:
```
grep --include=\*.{c,h} -rnw '/path/to/somewhere/' -e "pattern"
```
This will only search through the files which have .c or .h extensions. Another example:
```
grep --exclude=*.o -rnw '/path/to/somewhere/' -e "pattern"
```
Above will exclude searching all the files ending with .o extension. 

Just like exclude file it's possible to exclude/include directories through <code>--exclude-dir</code> and <code>--include-dir</code> parameter:
```
grep --exclude-dir={dir1,dir2,*.dst} -rnw '/path/to/somewhere/' -e "pattern"
```
<a name="egrep"></a>
# egrep
```
egrep -o 'X-CLIENT-IP-ADDRESS=\[[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+\]' all_grep_results | egrep -o '[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+' | sort | uniq
```
* <code>-o</code> just return the string that matches the pattern

<a name="run-process-in-background"></a>
# Running a process in background
## Way 1: the process is already running
If you run a process and later you realize that it will last very long, you can send it to execute in background although it's already running:
* Ctrl + Z: sends the process to background but stopped
* <code>fg</code>: the process continues execution
* <code>disown</code>: the process won't be killed although you close the session. You can't see the command's output anymore.

## Way 2: screen 
You must forsee that you might need to dettach a process.
* Run <code>screen</code>: you will enter screen.
* Run the process.
* Ctrl + AD: dettaches the process using <code>screen</code> (linux command). It keeps running in background.
* <code>screen -r</code>: to recover it. You will see the command's output.
* <code>screen -D -R screen_name</code>: to recover a screen which is attached somewhere (it dettaches the screen from that other place).

## Way 3: nohup
You must forsee that you might need to dettach a process.
With <code>nohup</code> the process runs in the background from the beginning of its execution:
```
nohup the_script &
```
<a name="curl"></a>
# curl
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

<a name="curl-send-file"></a>
## Send a file
```
curl -X POST -F data=@/path/to/file/name.txt localhost:8086/submitter/v1/submissions/{id}/runs/sequencing/csv
```
- **-F**: to emulate a form.
- **@**: prefix the file with **@** to attach it in the post as a file upload.

<a name="curl-performance"></a>
## Performance
```
curl -w %{time_starttransfer}\\n%{time_total}\\n -o /dev/null -s -X GET "localhost:9700/archiveservice/v1/users/123/files?sourceType=EBI_INBOX&limit=0"
```
<a name="netstat"></a>
# netstat
Proporciona informació dels **ports ocupats**.

Amb l'opció <code>-tapen</code> donarà informació sobre el procés (pid i nom) concret que està utilitzant cada port.

Així, si volem saber quina app està utilitzant un port determinat, hem d'executar:
```
sudo netstat -tapen | grep ":9400"
```
<a name="test-performance"></a>
# Test performance
<a name="performance-usb"></a>
## USB
1. Write a file (e.g. 10 GB) in the device.
1. Clear cache.
1. Read the file from the device.
```
> dd if=/dev/zero of=./largefile bs=1M count=10000
10000+0 records in
10000+0 records out
10485760000 bytes (10 GB, 9,8 GiB) copied, 100,449 s, 104 MB/s
> sudo sh -c "sync && echo 3 > /proc/sys/vm/drop_caches"
> dd if=./largefile of=/dev/null bs=1M
10000+0 records in
10000+0 records out
10485760000 bytes (10 GB, 9,8 GiB) copied, 109,784 s, 95,5 MB/s
```
<a name="performance-network"></a>
## Network
```
> speedtest-cli
Retrieving speedtest.net configuration...
Retrieving speedtest.net server list...
Testing from Consorci de Serveis Universitaris de Catalunya (84.88.66.194)...
Selecting best server based on latency...
Hosted by Eurona Wireless Telecom S.A. (Barcelona) [0.97 km]: 4.617 ms
Testing download speed........................................
Download: 900.13 Mbit/s
Testing upload speed..................................................
Upload: 346.88 Mbit/s
```

<a name="json-pretty-print"></a>
# JSON pretty print
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
<a name="link-to-app"></a>
# Create a link to an app
Open a terminal and type:
```
cd /usr/local/bin
sudo ln -s ../squirrel-sql-3.2.1/squirrel-sql.sh squirrel-sql
```
After that, you can run the program typing <code>squirrel-sql</code>
<a name="desktop-icon"></a>
# Create a Desktop icon
Open a terminal and type:
```
gnome-desktop-item-edit --create-new ~/Desktop
```
