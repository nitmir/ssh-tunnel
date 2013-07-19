#Ssh Tunnel

Ssh init script to setup multiple tunnel for port forwarding.

Just put one file (see the example.conf file) by tunnel in ```/etc/ssh_tunnel/```
and start stop earch tunnel or all by invoking ssh_tunnel.init.

Tunnels pid files are store by default in ```/var/run/ssh_tunnel/``` directory.


I personnaly use [monit](http://mmonit.com/monit/) to monitor tunnels with the following rules : 

<pre>
$ cat /etc/monit/conf.d/tunnels
# tunnel http to some machine
check process machine-http-tunnel with pidfile /var/run/ssh_tunnel/machine_http.pid
  start program = "/etc/init.d/ssh_tunnel start machine_http"
  stop program = "/etc/init.d/ssh_tunnel stop machine_http"
  if failed host localhost port 6666 protocol http timeout 30 seconds then restart
</pre>

the corresponding ssh_tunnel config file looks like : 
<pre>
$ cat /etc/ssh_tunnel/machine_http.conf
BIND=127.0.0.1
TYPE="L"
SPORT=6666
DEST="127.0.0.1:80"

KEY="/home/tunnel/.ssh/id_rsa"

SSH_DEST="pseudo@machine.example.net"

PROXY_COMMAND="/usr/bin/ssh -T -i $KEY -o NumberOfPasswordPrompts=0 -q login@other.domain.tld"
</pre>
