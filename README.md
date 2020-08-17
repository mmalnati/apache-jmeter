<h1>Configuration and installation of Apache Jmeter 3.3</h1>

Install Debian 8.8.0 (In nodes maester and slave)

- ```apt-get update```
- ```apt-get upgrade```

<h3>Install Java</h3>

- ```apt-get install software-properties-common```
- ```add-apt-repository "deb http://ppa.launchpad.net/webupd8team/java/ubuntu xenial main"```
- ```apt-get update```
- ```apt-get install oracle-java8-installer```
- ```javac -version```
- ```update-alternatives --config java```
- ```echo 'JAVA_HOME="/usr/lib/jvm/java-8-oracle"' >>/etc/environment```
- ```source /etc/environment```
- ```echo $JAVA_HOME```

<h3>Model of the distributed stress test Apache Jmeter 3.3</h3>

![](https://github.com/mmalnati/apache-jmeter/blob/master/jmeter.jpeg)

<h3>Install Apache Jmeter 3.3 (both nodes)</h3>

- ```wget http://apache.dattatec.com//jmeter/binaries/apache-jmeter-3.3.tgz```
- ```tar zxvf apache-jmeter-3.3.tgz```
- ```mv apache-jmeter-3.3 jmeter-3.3```
- ```jmeter-3.3/bin/jmeter –version```

Edit the master node. The file is located in /jmeter-3.3/bin/jmeter.properties and in the section remote_hosts add the ip adress of the slave nodes.

Install the library so you can execute the Apache Jmeter in graphical mode (X11) (master node only)
- ```apt-get install libxtst6 ```

<h3>Tunning of Jmeter</h3>

Edit the file and change the value
- ```vi /root/jmeter-3.3/bin/jmeter```
- ```HEAP="-Xms512m -Xmx6144m"```

<h3>Tunning Debian(both master and slave nodes)</h3>
<h4>Change in sysctl</h4>

Check the values in ip_local_port_range and then we change it

- ```cat /proc/sys/net/ipv4/ip_local_port_range= 32768   61000```
- ```net.ipv4.ip_local_port_range = 10000 65000```
- ```sysctl -p```

Edit the file /etc/hosts (master node only)

```
127.0.0.1 localhost
192.168.1.101  str1 stresstest-app01 stresstest-prod-app01
192.168.1.102  str2 stresstest-app02 stresstest-prod-app02
192.168.1.103  str3 stresstest-app03 stresstest-prod-app03
192.168.1.104  str4 stresstest-app04 stresstest-prod-app04
192.168.1.105  str5 stresstest-app05 stresstest-prod-app05
192.168.1.106  str6 stresstest-app06 stresstest-prod-app06
```

<h4>Delete rpcbind package</h4>

- ```apt-get --purge remove rpcbind```

<h4>Disable snmpd</h4>

- ```systemctl stop snmpd```
- ```systemctl disable snmpd```

<h4>Delete dependencies not used</h4>

- ```apt-get --purge autoremove```
<h3>Distributed load in JMeter</h3>

<h4>Start the slave nodes in server mode</h4>

- ```cd /jmeter-3.3/bin/```
- ```./jmeter-server```

In the graphical mode, generate the .jmx file for the test plan, save it, close the aplication, and then run the following command:

 ```./jmeter.sh -n -t /root/test-plan/plan_test.jmx -l /root/results.jtl -j /root/runlog.log -r```
- ``` -n --nongui: run JMeter in console mode```
- ``` -t --testfile <argument>: this is the file with the extension .jmx that will use for the test```
- ``` -l --logfile <argument>: this file will save the log samples (.jtl)```
- ``` -j --jmeterlogfile <argument>: generate a log file (.log)```
- ``` -r --runremote: start the remote nodes (as was defined in the remote_hosts)```

Once it is finished, run the graphical mode, create a Listener __“Summary Report”__ to load the .jtl file, so you can analize the results.
