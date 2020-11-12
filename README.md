# TPC-W-Benchmark
TPC-W is a popular transactional web benchmark which is used widely for performance benchmarking. TPC-W specifies a specification for a web e-Commerce web application. This repository contains an implementation of that specification using Java Servlets.

Source Code from : https://github.com/supunab/TPC-W-Benchmark

# Installation

# Setting up the requirements

First clone this repo into your local machine using the following command.

git clone https://github.com/SpyrosChouliaras/TPC-W-Benchmark.git

# Set up Java

Check java version:

```bash
java -version
```

I use openjdk version "1.8.0_272"

Add the path to the environment:

```bash
sudo nano /etc/environment
```
Add the Java_HOME

```bash
JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64/jre"
```

Add the ~/.bashrc file

```bash
sudo nano ~/.bashrc
```

and add:

```bash
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/jre
export PATH=$JAVA_HOME/bin:$PATH
```

finally source the ~/.bashrc file

```bash
source ~/.bashrc
```
# Install MySQL Database

```bash
sudo apt-get update

sudo apt-get install libmysqlclient20

sudo apt-get install mysql-server
```

Install MySQL database (I have used MySQL but can use any database which has a JDBC connector.) Then download the JDBC driver for MySQL database put it in the {tpc-w} directory.

# Install Tomcat Server

In order to run Java Servlets we need to install the Tomcat server (There are other Java Servlet engines as well). Installing Tomcat is just a matter of downloading and extracting the archive.


Before you download the tomcat add tomcat as a user:

```bash
sudo groupadd tomcat
sudo useradd -s /bin/fakse -g tomcat -d /opt/tomcat tomcat
```

Download the latest tomcat (I have downloaded the latest release of Tomcat 9) and extract it. 

Go to /opt directory:

```bash
cd /opt
```

1. Visit https://www.apache.org/

2. Projects

3. Tomcat

4. copy past the tar.gz link address and add it to wget command

```bash
wget https://www.mirrorservice.org/sites/ftp.apache.org/tomcat/tomcat-9/v9.0.39/bin/apache-tomcat-9.0.39.tar.gz
```

Now you have to extract the file in /opt directory:

```bash
sudo tar -xzvf apache-tomcat-9.0.39.tar.gz
```
Now you have to change the owenership of tomcat directory to the tomcat user and group@

```bash
sudo chown -R tomcat:tomcat /opt/tomcat
```

Also give the execute permission to the directory under the Tomcat

```bash
sudo chmod +x /opt/tomcat/bin/
```

Add the Catalina home to ~/.bashrc file

```bash
sudo nano ~/.bashrc
```

Add the following line to the ~/.bashrc file
```bash
export CATALINA_HOME=/opt/tomcat
```

again source the ~/.bashrc file

```bash
source ~/.bashrc
```

See if the Catalina is set properly by running in the terminal the following commnad:

```bash
echo $CATALINA_HOME
```

The output should be : /opt/tomcat

You can start the Tomcat server:

```bash
cd /opt/tomcat/bin
sudo ./startup.sh
```

Then visit https://localhost:8080 or visit the Server remotely https://IP_address:8080

Now run Tomcat as a service:

Stop the server:

```bash
sudo ./shutdown.sh
```

Change ownership of Tomcat directory

```bash
sudo chown -hR tomcat:tomcat /opt/tomcat
```

Go to system directory:

```bash
cd /etc/systemd/system/
```

Create apache-tomcat.service file

```sudo nano apache-tomcat.service

Add the following configuration:

[Unit]
Description=Apache Tomcat 9 Servlet Container
After=syslog.target network.target

[Service]
User=tomcat
Group=tomcat
Type=forking
Environment=CATALINA_PID=/opt/tomcat/tomcat.pid
Environment=CATALINA_HOME=/opt/tomcat
Environment=CATALINA_BASE=/opt/tomcat
ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Then :

```bash
systemctl daemon-reload
```

After that you should be able to start Tomcat by using system control service:

```bash
systemctl start apache-tomcat
```

**Hint**

if you want to access Tomcat manager from another machine do the following:

```bash 
cd /opt/tomcat/conf/Catalina/localhost
```

and add a manager.xml file as below:

```bash
<Context privileged="true" antiResourceLocking="false" 
         docBase="${catalina.home}/webapps/manager">
    <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="^.*$" />
</Context>
```

Then add to tomcat-users.xml file and add a user:

```bash
sudo nano /opt/tomcat/conf/tomcat-users.xml
```

And add in the ned before </tomcat-users>

```bash
...
...
...
...
<user username="admin" password="password" roles="manager-gui"/>
</tomcat-users>
```
# Setting up Configurations

**Configure TPC-W**

cd ../java-tpcw/

nano tpcw.properties 
And change the JDBC settings like this:

#jdbc.path=jdbc\:mysql\://localhost\:3306/std?user\=tpcw&password\=pswd
jdbc.path=jdbc\:mysql\://10.11.0.6\:3306/std?user\=tpcw&password\=pswd
Change the num.item and num.eb as you like. For example:

num.item=100000
num.eb=100

You need configure some properties using tpcw.properties and main.properties files which is located in the tpc-w directory. tpcw.properties contains properties directly related to TPC-W web application whereas main.properties contains other properties. Go ahead and update the properties in both files. (The properties are self explanatory.)

To set the java packages into the main.property file. So first run these two commands and take note of the full name of these two packages.

```bash
ls /usr/share/java/*servlet*
ls /usr/share/java/*mysql*
```

Then add the paths inside main.properties:

#<!-- Path to servlet.jar, change this ... -->
cpServ=/usr/share/java/servlet-api-3.0.jar

#<!-- Path to the JDBC driver for your DBMS, change this ... -->
cpJDBC=/usr/share/java/mysql.jar

#<!-- Directory where tpcw.war will be put with task 'inst' -->
webappDir=/var/lib/tomcat7/webapps

Inside tpcw.properties:
You may need to change the user and password of the jdbc.path. Also you might want to adjust the jdbc.connPoolMax according to your requirement.

**Configure MySQL**

After these adjustments are done, navigate to /etc/mysql/mysql.conf.d and run

```bash
sudo nano mysqld.cnf
```

When inside mysqld.cnf, uncomment max_conections and put the same number you put in jdbc.connPoolMax of the tpcw.properties file. Then add the following line to the mysqld.cnf in a new line.

```bash
sql_mode='ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION'
```

Now you can save the changes and exit the file.

Then you will have to restart the mysql server.

```bash
sudo systemctl restart mysql
```

Now log into mysql using

```bash
mysql -u root -p
```

and create a database named tpcw

```bash
CREATE DATABASE tpcw;
```
Then you can exit mysql by typing 'exit'.

# Building TPC-W

If you have tried ant building process before, run the following command. This will delete all the file in the src, build, and dist folders which are generated by the building process. (Note You can skip this step if you are building this for the first time.)

```bash
ant clean
```

Run the following command to make source files with given ant properties. This will generate the source code in the src directory.

```bash
ant mksrc
```
Then compile the files using the following commands. This will build the servlets, RBEs (Remote Browser Emulators, i.e. Client workload generators) and populate class (to populate the database) in the build directory.

```bash
ant build
```

Then create the tpcw.war using the following command. This will create the .war file in the dist directory.

```bash
ant dist
```
You can generate the javadocs using the following command.

```bash
ant docs
```
NOTE
Before proceeding further, make sure that you are running the the tomcat server. (Use the scripts in startup.sh and shutdown.sh scripts in the bin folder of tomcat to start and stop the tomcat server respectively.)

Now let's copy the tpcw.war file to the Tomcat webapps directory. You can do this by running the following command.

```bash
ant inst
```
Use the following command to populate the database. (Make sure you have specified the correct username, password and hostname in the main.properties file.)

```bash
ant gendb
```
Then, run the following command to generate images and copy to the tpcw web application directory. (This directory {tomcat-directory}/webapps/tpcw will be generated after you run the tpcw.war using ant inst command.)

```bash
ant genimg
```
Then go to the following directory inside the {tomcat-directory}/webapps/tpc-w/WEB-INF. Create a folder named lib inside this directory. The copy the mysql driver (mysql-connector-java-5.1.47.jar) file to the lib folder.

Now restart the tomcat server (shutdown and start) and the TPC-W web application should be runnig.

You can test whether it is running by accesing http://localhost:8080/tpcw/TPCW_home_interaction using a web browser. (Make sure to replace the link with the correct ip and the port.)

# Using the benchmark

Now that the tpc-w server is up and running (if not, please see the above section and set it up), let's see how to run performance tests using Remote Browser Emulators (RBEs).

Go to the dist folder which is located in the directory you cloned the git repo. This folder contain the Java files for RBE.

Run the following command to get to know about the command line arguments needed to emulate clients.

java rbe.RBE
This will give a quick overview of how to run the clients. In order to specify the type of workload mix (Browsing, Shopping, or Ordering) you can use the following as EB Factory argument.

Browsing Mix = rbe.EBTPCW1Factory
Shopping Mix = rbe.EBTPCW2Factory
Ordering Mix = rbe.EBTPCW3Factory

The follwing command shows an example case. This runs 400 concurrent users with Browsing workload mix. The ramp-up time is 60 seconds, measuring interval (interval in which the perfomance metrics are measured) is 300 seconds and there is a ramp-down (warm-down) period of 60 seconds.

```bash
java rbe.RBE -EB rbe.EBTPCW1Factory 400 -OUT data.m -RU 60 -MI 360 -RD 60 -ITEM 1000 -TT 0.1 -MAXERROR 0 -WWW http://IP_address:8080/tpcw/

```
