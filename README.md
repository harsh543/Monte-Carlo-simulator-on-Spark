# Monte-Carlo-simulator-on-Spark

# Purpose #

Create a private cloud using Raspberry PIs (ARM architecture) and use Kubernetes 
for container orchastration to run MapReduce jobs using Apache Hadoop.  

### What is this repository for? ###

* A how-to guide for construction of cluster.
* Provide the resources required for the construction of the cluster. 

### Hardware required ###

* Raspberry PIs [[Buy](https://www.amazon.com/Raspberry-Pi-RASPBERRYPI3-MODB-1GB-Model-Motherboard/dp/B01CD5VC92)]
* Power suppy [[Buy](https://www.amazon.com/CanaKit-Raspberry-Supply-Adapter-Charger/dp/B00MARDJZ4/ref=pd_bxgy_147_2?_encoding=UTF8&psc=1&refRID=6M8CYG5AKF2YM9H09JBN)]
* SD card [[Buy](https://www.amazon.com/Samsung-MicroSD-Adapter-MB-ME32GA-AM/dp/B06XWN9Q99/ref=pd_bxgy_147_3?_encoding=UTF8&psc=1&refRID=6M8CYG5AKF2YM9H09JBN)]

### Assumptions ###

* We have 8 Raspberry PIs (1 Master, 7 Slaves).


## STEPS

#### 1. Wifi connection, [(https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/setting-up-wifi-with-occidentalis) ](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/setting-up-wifi-with-occidentalis)

#### 2. Change hostname [(https://www.howtogeek.com/167195/how-to-change-your-raspberry-pi-or-other-linux-devices-hostname/)](https://www.howtogeek.com/167195/how-to-change-your-raspberry-pi-or-other-linux-devices-hostname/)

#### 3. Create Swap partition as the Ram is less [(https://www.centos.org/docs/5/html/5.2/Deployment_Guide/s2-swap-creating-file.html)]
(https://www.centos.org/docs/5/html/5.2/Deployment_Guide/s2-swap-creating-file.html)

#### 4. pull base docker image of Ubuntu ARM [(https://hub.docker.com/r/armv7/armhf-ubuntu/)](https://hub.docker.com/r/armv7/armhf-ubuntu/)

#### 5. Open docker terminal and type
``` docker run -it -expose amr7/ubuntu:latest bash```

#### 6. add apt-get
```apt install software-properties-common ```

#### 7. Install Java8 [http://www.webupd8.org/2012/09/install-oracle-java-8-in-ubuntu-via-ppa.html](http://www.webupd8.org/2012/09/install-oracle-java-8-in-ubuntu-via-ppa.html)

#### 8. Install nano
```apt install nano ```

#### 9. Install Open ssh client
```apt install openssh-client```

#### 10. Install Open ssh server
```
apt install openssh-server
```

#### 11. Start ssh server and enable password less ssh login for root
```
/etc/init.d/ssh start
ssh-keygen -t rsa -P ""
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
ssh localhost 
```

#### 12. change hostname to master
```
nano /etc/hosts
ssh master 
```

#### 13. Install Hadoop [https://raspberrypicloud.wordpress.com/2013/04/25/getting-hadoop-to-run-on-the-raspberry-pi/](https://raspberrypicloud.wordpress.com/2013/04/25/getting-hadoop-to-run-on-the-raspberry-pi/)

#### 14. Install Hive [https://www.edureka.co/blog/apache-hive-installation-on-ubuntu](https://www.edureka.co/blog/apache-hive-installation-on-ubuntu)

#### 15. Install Spark [https://medium.com/@josemarcialportilla/installing-scala-and-spark-on-ubuntu-5665ee4b62b1](https://medium.com/@josemarcialportilla/installing-scala-and-spark-on-ubuntu-5665ee4b62b1)

#### 16. Install Scala [https://www.scala-lang.org/download/](https://www.scala-lang.org/download/)

#### 17. Copy Input and project Jar files to Raspberry Pi
```
pscp -r C:\Users\Ragha\Desktop\filesToCopy\MyMonte\input pirate@192.168.1.29:
```

#### 18. Copy Input and project Jar files to Docker
```
docker cp input 1ba3e9b11390:/root/
```

#### 19. Start the Hdfs and Yarn services
```
cd /usr/local/hadoop/etc/hadoop/start-all.sh
```

#### 20. Start the Spark Daemons
```
spark/sbin/start-all.sh
```

#### 21. Copy input files to hdfa
```
hdfs dfs -put /root/input/* /user/root/Companies/
hdfs dfs -put /root/finance/* /user/root/Finance/
```

#### 22. submit the spark job
``` 
 spark-submit --class com.hortonworks.example.Main --master yarn --deploy-mode cluster 
 --jars spark/lib/datanucleus-api-jdo-3.2.6.jar,spark/lib/datanucleus-core-3.2.10.jar,spark/lib/datanucleus-rdbms-3.2.9.jar,spark/lib/spark-1.6.3-yarn-shuffle.jar 
 --name monte-carlo MyMonte/monte-carlo-var-1.0-SNAPSHOT.jar Companies/* Finance/* Output
```
 
#### 23. Output on the HDFS and copy
```
hdfs dfs -cat /usr/local/Output/
hdfs dfs -get Output/* /usr/local/Output/
``` 

#### Final Image 
```
docker pull raghavendrasomannavar/montecarlo:1.0.0
