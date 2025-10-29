## PostgreSQL Database installation in amazon Linux
 
```
sudo yum update -y
```
# java 17 installation
```
sudo dnf install -y java-17-amazon-corretto
```

java -version
# Postgressql installation 

```
sudo dnf install -y postgresql15 postgresql15-server postgresql15-contrib
```
```
sudo mkdir -p /var/lib/pgsql/15/data
```
```

sudo chown -R postgres:postgres /var/lib/pgsql
```
```

sudo -u postgres /usr/bin/initdb -D /var/lib/pgsql/15/data
```
sudo systemctl status postgresql-15
```
sudo vi /etc/systemd/system/postgresql-15.service
-------------------------------------------------
```

[Unit]
Description=PostgreSQL 15 database server
After=network.target

[Service]
Type=forking
User=postgres
ExecStart=/usr/bin/pg_ctl -D /var/lib/pgsql/15/data start
ExecStop=/usr/bin/pg_ctl -D /var/lib/pgsql/15/data stop
ExecReload=/usr/bin/pg_ctl -D /var/lib/pgsql/15/data reload
PIDFile=/var/lib/pgsql/15/data/postmaster.pid

[Install]
WantedBy=multi-user.target

```


# run below commands 
```

sudo systemctl daemon-reload
sudo systemctl enable postgresql-15
sudo systemctl start postgresql-15
sudo systemctl status postgresql-15
```


sudo vi /var/lib/pgsql/15/data/pg_hba.conf
Find these lines and make them look like this:
```

# "local" is for Unix domain socket connections only
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            trust
# IPv6 local connections:
host    all             all             ::1/128                 trust

```

```
sudo systemctl restart postgresql-15
```
```

sudo -iu postgres
```
```
psql

```

```

CREATE DATABASE sonarqube;
CREATE USER sonar WITH ENCRYPTED PASSWORD '12345';
GRANT ALL PRIVILEGES ON DATABASE sonarqube TO sonar;
\q

```
```

exit

```


## SonarQube installation 
```
cd /opt
sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.6.0.92116.zip
sudo dnf install -y unzip
sudo unzip sonarqube-10.6.0.92116.zip
sudo mv sonarqube-10.6.0.92116 sonarqube
sudo rm -f sonarqube-10.6.0.92116.zip
```
```
sudo useradd sonar
sudo chown -R sonar:sonar /opt/SonarQube
```
```
sudo vim /opt/sonarqube/conf/sonar.properties
```

# add below lines are commented lines uncomment and add value 
```

# Database Configuration
sonar.jdbc.username=sonar
sonar.jdbc.password=12345
sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube

# Web Configuration
sonar.web.host=0.0.0.0
sonar.web.port=9000

```

```

sudo sysctl -w vm.max_map_count=262144

echo "vm.max_map_count=262144" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

sudo su - sonar

cd /opt/sonarqube/bin/linux-x86-64

./sonar.sh start

./sonar.sh status
```


if you base issue for running SonarQube check below file logs to see error
########################################################################

```

tail -n 50 /opt/sonarqube/logs/sonar.log

tail -n 50 /opt/sonarqube/logs/web.log

```











