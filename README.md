<h1>Configuración e instalación de Apache Jmeter 3.3</h1>

Instalación en Debian 8.8.0 (En nodo Maestro y esclavos)

- ```apt-get update```
- ```apt-get upgrade```

<h3>Instalamos Java</h3>

- ```apt-get install software-properties-common```
- ```add-apt-repository "deb http://ppa.launchpad.net/webupd8team/java/ubuntu xenial main"```
- ```apt-get update```
- ```apt-get install oracle-java8-installer```
- ```javac -version```
- ```update-alternatives --config java```
- ```echo 'JAVA_HOME="/usr/lib/jvm/java-8-oracle"' >>/etc/environment```
- ```source /etc/environment```
- ```echo $JAVA_HOME```

<h3>Modelo para el modo distribuido de stress test en Apache Jmeter 3.3</h3>

![](https://github.com/mmalnati/apache-jmeter/blob/master/jmeter.jpeg)

<h3>Instalación de Apache Jmeter 3.3 (ambos nodos)</h3>

- ```wget http://apache.dattatec.com//jmeter/binaries/apache-jmeter-3.3.tgz```
- ```tar zxvf apache-jmeter-3.3.tgz```
- ```mv apache-jmeter-3.3 jmeter-3.3```
- ```jmeter-3.3/bin/jmeter –version```

Editar en el nodo maestro, el archivo /jmeter-3.3/bin/jmeter.properties y en la sección remote_hosts agregar las ip de los nodos esclavos.

Instalar la siguiente librería para ejecutar de manera remota por X11(sólo en nodo Maestro)
- ```apt-get install libxtst6 ```

<h3>Tunning en Jmeter</h3>

Editar el archivo jmeter y cambiar el siguiente valor 
- ```vi /root/jmeter-3.3/bin/jmeter```
- ```HEAP="-Xms512m -Xmx6144m"```

<h3>Tunning Debian(para nodo Maestro y esclavos)</h3>
<h4>Cambios en sysctl</h4>

Revisamos los valores del archivo ip_local_port_range y lo cambiamos

- ```cat /proc/sys/net/ipv4/ip_local_port_range= 32768   61000```
- ```net.ipv4.ip_local_port_range = 10000 65000```
- ```sysctl -p```

Editamos el archivo /etc/hosts (sólo el nodo Maestro)
```
127.0.0.1 localhost 
192.168.1.101  str1 stresstest-app01 stresstest-prod-app01
192.168.1.102  str2 stresstest-app02 stresstest-prod-app02
192.168.1.103  str3 stresstest-app03 stresstest-prod-app03
192.168.1.104  str4 stresstest-app04 stresstest-prod-app04
192.168.1.105  str5 stresstest-app05 stresstest-prod-app05
192.168.1.106  str6 stresstest-app06 stresstest-prod-app06
```
<h4>Eliminamos el rpcbind</h4>

- ```apt-get --purge remove rpcbind```

<h4>Deshabilitamos en snmpd</h4>

- ```systemctl stop snmpd```
- ```systemctl disable snmpd```

<h4>Borramos programas no utilizados</h4>

- ```apt-get --purge autoremove```
<h3>Carga distribuida de JMeter</h3>

<h4>Iniciar los nodos esclavos en modo servidor</h4>

- ```cd /jmeter-3.3/bin/```
- ```./jmeter-server```

En la interfaz gráfica, generar el archivo para realizar el plan de test  .jmx, y luego de cerrar la aplicación,  desde la consola correr el siguiente comando:

- ```./jmeter.sh -n -t /root/test-plan/plan_test.jmx -l /root/resultados.jtl -j /root/runlog.log -r```
- ``` -n --nongui: corre JMeter en modo consola```
- ``` -t --testfile <argument>: el archivo .jmx que se usará para correr el stress test```
- ``` -l --logfile <argument>: el archivo que guarda los log samples (.jtl)```
- ``` -j --jmeterlogfile <argument>: generar un  log file (.log)```
- ``` -r --runremote: iniciar los nodos remotos (como se definió en remote_hosts)```

Una vez terminado, ingresar a jmeter por gui, y crear el Listener “Summary Report” para poder leer los archivos .jtl

