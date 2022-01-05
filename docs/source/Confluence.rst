#Confluence setup:  

##DB:


####Download:

- download mysql80-community-release

\# sudo rpm -ivh mysql80-community-release-el7-3.noarch.rpm  
\# sudo rpm -pql mysql80-community-release-el7-3.noarch.rpm  
\# sudo md5sum mysql80-community-release-el7-3.noarch.rpm  
\# sudo grep 'temporary password' /var/log/mysqld.log  
\# sudo mysql_secure_installation

- Connect to the database:  
\# mysql  -h 127.0.0.1   -P 3308 -u netset -pword -e "SHOW VARIABLES LIKE '%max_connections%';"  
\# mysql -u root

Note1: I use JNDI datasource connection 

#####SHOW in DATABASE:

\> SHOW VARIABLES LIKE 'innodb_log_file_size';  
\> SHOW VARIABLES LIKE 'max_allowed_packet';  
\> SHOW VARIABLES LIKE '%max_connections%';  
\> SHOW VARIABLES LIKE '%innodb_strict_mode%'; for Zabbix server  
\> SHOW VARIABLES LIKE '%validate_password%';  
\> show global variables like 'local_infile';  
\> SELECT DEFAULT_CHARACTER_SET_NAME, DEFAULT_COLLATION_NAME FROM information_schema.SCHEMATA WHERE schema_name = 'confluence';  
\> SELECT title, lastmoddate FROM CONTENT group by lastmoddate;  
\> SELECT User, Host, plugin, authentication_string FROM mysql.user;  

SHOW PRIVILIGES:

\> show grants for 'netset'@'localhost';  
\> SET GLOBAL local_infile=1;

SHOW tatal nember of tables:

\>SELECT count(*) AS TOTALNUMBEROFTABLES FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA = 'confluence';

#####Modify DATABASE:

\> CREATE DATABASE confluence character set utf8 collate utf8_bin; <br/>
\> ALTER DATABASE confluence CHARACTER SET utf8 COLLATE utf8_bin; <br/>
\> CREATE USER 'netset'@'localhost' IDENTIFIED BY 'word'; <br/>
\> GRANT ALL ON confluence.* TO 'netset'@'localhost';  
\> FLUSH PRIVILEGES;

\> UPDATE user SET plugin='mysql_native_password' WHERE User='netset';  
\> ALTER USER 'root'@'localhost' IDENTIFIED BY '';  
OR  
\> UPDATE mysql.user SET authentication_string = PASSWORD('') WHERE User = 'netset' AND Host = 'localhost';  
OR  
\> SET PASSWORD FOR 'netset'@'localhost' = PASSWORD('');

\> SET GLOBAL mysqlx_max_connections = 300;  
\> SET GLOBAL max_connections = 300;  
\> SET GLOBAL max_connections = 500;  
\> SET GLOBAL innodb_log_file_size=2000000;  
\> SET GLOBAL max_allowed_packet=1073741824;  
\> SET GLOBAL sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,
                       ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';  
\> SET session sql_mode="STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,
                         ERROR_FOR_DIVISION_BY_ZERO,
                         NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION";  

\> SET GLOBAL validate_password.policy=LOW;  
\> SET GLOBAL validate_password.mixed_case_count = 0;  
\> SET GLOBAL validate_password.number_count = 0;  
\> SET GLOBAL validate_password.check_user_name = 0;  
\> SET GLOBAL validate_password.special_char_count = 0;  
\> SET GLOBAL validate_password.LENGTH = 0;  



ALTER TABLE table_name  
 \>  DROP COLUMN column_name;  

#####Modify database in conf file:

[mysqld]

validate_password.length=0
validate_password.mixed_case_count=0
validate_password.number_count=0
validate_password.policy=LOW
validate_password.special_char_count=0
default-storage-engine=INNODB

innodb_log_file_size=2GB
max_allowed_packet=256M
binlog_format=row
character-set-server=utf8
collation-server=utf8_bin

sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION

==============================================================   

Free the memory from caches first level:  
\# sync; echo 1 > /proc/sys/vm/drop_caches

==============================================================  
## App:

1- Add work directory (Home directory) to the installation directory:    
\# nano -w  atlassian-confluence-7.11.1/confluence/WEB-INF/classes/confluence-init.properties
confluence.home=/home/confluence/confluence-7.10.2.1  
2- copy the database connector:  
\# cp confluence/confluence/WEB-INF/lib/mysql-connector-java-5.1.47.jar atlassian-confluence-7.11.1/confluence/WEB-INF/lib/  
3- copy the sever conf file:  
\# cp confluence/conf/server.xml atlassian-confluence-7.11.1/conf/  

Note: jdbc:mysql://localhost:3306/db?allowPublicKeyRetrieval=true&amp;useSSL=false  

4- unlink confluence and link new confluence:  

\# ln -s atlassian-confluence-7.11.1 confluence  

4- 
sudo rm -r logs/*
sudo rm -r temp/*
sudo rm -r plugins-cache/*
sudo rm -r plugins-osgi-cache/*
sudo rm -r plugins-temp/*

6-

CATALINA_OPTS="-Xms1024m -Xmx6000m -XX:+UseG1GC ${CATALINA_OPTS}"
JRE_HOME="/opt/jre11"

7-
chown -R confluence:confluence atlassian-confluence-7.11.1
chown -R confluence:confluence /home/confluence/confluence-7.11.1/


#################################################

jar -xf confluence-7.11.1.jar
and change

nano -w databaseSubsystemContext.xml

DESCRIBE [table name];

DENORMALISED_SPACE_VIEW_PERMISSIONS
DENORMALISED_SPACE_EDIT_PERMISSIONS
DENORMALISED_SID

DROP table MIG_EXCLUDE_APP;
           MIG_APP_ACCESS_SCOPE;

SELECT max(lastmoddate) FROM ATTACHMENTS;
select TITLE, LASTMODIFIER, LASTMODDATE, USERNAME from CONTENT group by TITLE;
 select * from CONTENT LIMIT 1;

SELECT email_address AS "email", user_name AS "username", display_name AS "displayname", active AS "active" FROM cwd_user;

#################################################
Configuration:

1- JAVA 8 ELLER JAVA 11
1- JAVA eller JRE
2- MYSQL ELLER MARIADB
3- MYSQL 5.7 eller 8.0.23
4- mysql-connector-java-8.0.23.jar (saknar datetimelocal )eller äldre 5.1.47
5- xmx=6000
6- Attachments folder in HOME_dir 
