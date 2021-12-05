Solution for Zabbix on Week 6 - Day 1

### Tasks:
#### 1. Install Zabbix Server 5 on VM that includes:
- Zabbix Server
- Database
- Apache
- PHP
- Zabbix Server-Modules for Apache & Database
- Zabbix Frontend

##### Installing Zabbix Server, MySQL as Database, Apache2, PHP, Zabbix Frontend, Zabbix Server Module for MySQL & Apache2
```shell
# apt install zabbix-server-mysql mysql-server apache2 libapache2-mod-php php-mysql zabbix-frontend-php

tom@kb1:~$ wget https://repo.zabbix.com/zabbix/5.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_5.4-1+ubuntu20.04_all.deb -O zabbix
tom@kb1:~$ dpkg -i ./zabbix
tom@kb1:~$ apt update
tom@kb1:~$ apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent
```

![image](https://user-images.githubusercontent.com/23631617/144730771-b7d9cac7-f531-4b4d-98b2-45cd028cfef3.png)

##### Zabbix Config File
```
LogFile=/var/log/zabbix/zabbix_server.log
LogFileSize=0
PidFile=/run/zabbix/zabbix_server.pid
SocketDir=/run/zabbix
DBName=zabbix
DBUser=zabbix
DBPassword=assignment
SNMPTrapperFile=/var/log/snmptrap/snmptrap.log
Timeout=4
FpingLocation=/usr/bin/fping
Fping6Location=/usr/bin/fping6
LogSlowQueries=3000
StatsAllowedIP=127.0.0.1
```

##### Setting up Zabbix DB and User
```SQL
CREATE DATABASE zabbix CHARACTER SET utf8 collate utf8_bin;
CREATE USER zabbix@localhost iDENTIFIED BY '********';
GRANT ALL PRIVILEGES ON zabbix.* TO zabbix@localhost;
```

```shell
tom@kb1:~$ zcat /usr/share/doc/zabbix-sql-scripts/mysql/create.sql.gz | mysql -uzabbix -p zabbix
```

---

#### 2. Provide screenshot of
- Running Zabbix Server service,
- Database config parameter in server config file
- Server Dashboard

##### Running Zabbix Server Service
```shell
tom@kb1:~$ sudo systemctl restart zabbix-server zabbix-agent apache2
tom@kb1:~$ sudo systemctl status zabbix-server.service 
```

![image](https://user-images.githubusercontent.com/23631617/144731469-88912051-77a2-4195-8528-3648a9b4601b.png)

##### Database config parameter in server config file
![image](https://user-images.githubusercontent.com/23631617/144731407-284eb0a6-7032-49d2-aaa6-f9b08be4de36.png)

```
...
DBName=zabbix
DBUser=zabbix
DBPassword=assignment
...
```

##### Zabbix Web Interface
![image](https://user-images.githubusercontent.com/23631617/144731705-1b9253c0-f15f-4639-8648-150dabf831e0.png)

##### Server Dashboard
![image](https://user-images.githubusercontent.com/23631617/144731955-7e0fa355-932d-4e8d-b6e8-9079217c95c3.png)

---

3. Install Latest Zabbix Agent on VM or host machine or server itself to fetch logs, steps include:
- Run as active check agent
- Add a logging item to the same template for fetching /var/log/syslog(Ubuntu) or /var/log/messages
(CentOS)
- Fetch those logs from the host (Make sure required permissions are set for zabbix-agent to
pull logs)
- Provide agent configuration file & screenshots for target machine graph & logs

##### Run as active check agent
![image](https://user-images.githubusercontent.com/23631617/144732096-d9924fb3-930e-4d78-bfce-41843e8f17a9.png)

##### Giving syslog permission to user `zabbix`
```console
tom@kb1:~$ sudo usermod -aG syslog zabbix
tom@kb1:~$ groups zabbix
```
![image](https://user-images.githubusercontent.com/23631617/144732169-5c9346e2-5285-45af-8ec9-d42ff7790a96.png)

##### Creating Host
![image](https://user-images.githubusercontent.com/23631617/144732683-ef0c375f-e9a8-4344-a036-6d9d99667afc.png)

##### Adding Active Agent Template
![image](https://user-images.githubusercontent.com/23631617/144732694-5f5673a4-40ad-4ae3-991c-f45b078f280a.png)

##### Add a logging item to the same template for fetching /var/log/syslog(Ubuntu) or /var/log/messages (CentOS)
![image](https://user-images.githubusercontent.com/23631617/144732971-d7210bd7-b63e-4b65-956d-a9ffc399c107.png)

##### Fetch those logs from the host
![image](https://user-images.githubusercontent.com/23631617/144733274-e6e2e2a2-39aa-4fce-850f-0afd132353f3.png)

##### Agent Configuration File

```
# /etc/zabbix/zabbix_agentd.conf
PidFile=/run/zabbix/zabbix_agentd.pid
LogFile=/var/log/zabbix/zabbix_agentd.log
LogFileSize=0
Server=127.0.0.1
ServerActive=127.0.0.1
Hostname=srv0
Include=/etc/zabbix/zabbix_agentd.d/*.conf
```

##### Target Machine Graph and Logs

* Used Space
![image](https://user-images.githubusercontent.com/23631617/144733425-f329574d-7c76-4bf7-aa3d-67c7f6d17b7c.png)

* CPU Idle Time
![image](https://user-images.githubusercontent.com/23631617/144733440-800cb2fe-453b-4d31-974a-0a805e7be2c2.png)

* Load Average
![image](https://user-images.githubusercontent.com/23631617/144733456-7c7a02e6-8183-4c72-8930-6925988e09f1.png)

* Software Installed Log
![image](https://user-images.githubusercontent.com/23631617/144733509-91cc3f75-f83d-4b43-8511-8ffc8295ef72.png)

* System Log
![image](https://user-images.githubusercontent.com/23631617/144733531-114b89be-19a7-4286-b455-a250aae1d1c5.png)
