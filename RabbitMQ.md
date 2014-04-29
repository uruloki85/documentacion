[RabbitMQ](http://www.rabbitmq.com/documentation.html)
==========

Consultes amb <code>rabbitmqctl</code>
--------------------------------------
* You can use rabbitmqctl to print the messages_unacknowledged field:
<pre><code>sudo rabbitmqctl list_queues name messages_ready messages_unacknowledged</code></pre>
<pre><code>Listing queues ...
hello    0       0
...done.</code></pre>
Això serveix per detectar que no s'estan enviant l'ACK i, per tant, no s'estan eliminant els missatges de la cua. 
