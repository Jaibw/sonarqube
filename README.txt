# install java 
sudo apt update
sudo apt-get install openjdk-11-jdk -y

# install postgres 
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >>  /etc/apt/sources.list.d/pgdg.list'
wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add - 
sudo apt install postgresql postgresql-contrib -y 
sudo systemctl enable postgresql
sudo systemctl start postgresql
sudo su - postgres
createuser sonar
psql
   ALTER USER sonar WITH ENCRYPTED password 'password';
   CREATE DATABASE sonarqube OWNER sonar;
   GRANT ALL PRIVILEGES ON DATABASE sonarqube to sonar;
   \q
exit

# install sonarqube 
sudo apt-get install zip -y
sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-7.2.zip
sudo unzip sonarqube-7.2.zip
sudo mv sonarqube-7.2 /opt/sonarqube

# configure sonarqube 
sudo groupadd sonar
sudo useradd -d /opt/sonarqube -g sonar sonar
sudo chown sonar:sonar /opt/sonarqube -R
sudo nano /opt/sonarqube/conf/sonar.properties
  sonar.jdbc.username=sonar
  sonar.jdbc.password=password
  sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube
  Ctrl + x : Save and exit the file.
sudo nano /opt/sonarqube/bin/linux-x86-64/sonar.sh
  # About 50 lines down, locate this line:
  RUN_AS_USER=sonar
  Ctrl + x : Save and exit the file.
sudo vim /etc/systemd/system/sonar.service

Press i to switch insert mode 
  
[Unit]
Description=SonarQube service
After=syslog.target network.target
[Service]
Type=forking
ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop
User=sonar
Group=sonar
Restart=always
LimitNOFILE=65536
LimitNPROC=4096
[Install]
WantedBy=multi-user.target

Press: Esc and press :wq   - Save and exit the file.


sudo systemctl enable sonar
sudo systemctl start sonar
sudo systemctl status sonar    type q to exit 

sudo vim /etc/sysctl.conf
Add the following lines so press i to switch in insert mode 

vm.max_map_count=262144
fs.file-max=65536
ulimit -n 65536
ulimit -u 4096

Press: Esc and press :wq   - Save and exit the file.

sudo reboot




sudo systemctl start sonar
sudo systemctl status sonar





