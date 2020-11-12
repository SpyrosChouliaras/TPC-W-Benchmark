# TPC-W-Benchmark
TPC-W is a popular transactional web benchmark which is used widely for performance benchmarking. TPC-W specifies a specification for a web e-Commerce web application. This repository contains an implementation of that specification using Java Servlets.

Source Code from : https://github.com/supunab/TPC-W-Benchmark

# Installation

# Setting up the requirements

First clone this repo into your local machine using the following command.

git clone https://github.com/SpyrosChouliaras/TPC-W-Benchmark.git

# Install Tomcat Server

In order to run Java Servlets we need to install the Tomcat server (There are other Java Servlet engines as well). Installing Tomcat is just a matter of downloading and extracting the archive.

Download the latest tomcat (I have downloaded the latest release of Tomcat 9) and extract it.

You also need to install java and set homepath 

# Install MySQL Database

```bash
sudo apt-get update

sudo apt-get install libmysqlclient20

sudo apt-get install mysql-server
```

Install MySQL database (I have used MySQL but can use any database which has a JDBC connector.) Then download the JDBC driver for MySQL database put it in the {tpc-w} directory.

# Setting up Configurations

You need configure some properties using tpcw.properties and main.properties files which is located in the tpc-w directory. tpcw.properties contains properties directly related to TPC-W web application whereas main.properties contains other properties. Go ahead and update the properties in both files. (The properties are self explanatory.)

Inside main.properties:

#<!-- Path to servlet.jar, change this ... -->
cpServ=/usr/share/java/servlet-api-3.0.jar

#<!-- Path to the JDBC driver for your DBMS, change this ... -->
cpJDBC=/usr/share/java/mysql.jar

#<!-- Directory where tpcw.war will be put with task 'inst' -->
webappDir=/opt/tomcat/webapps

Inside tpcw.properties:
You may need to change the user and password of the jdbc.path. Also you might want to adjust the jdbc.connPoolMax according to your requirement.

After these adjustments are done, navigate to /etc/mysql/mysql.conf.d and run
