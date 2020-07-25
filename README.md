# sonarqube
how to install sonarqube
#How to install SonarQube on Ubuntu | Install Sonarqube 7.7 on Ubuntu 18.0.4 with PostgreSQL
SonarQube is one of the popular static code analysis tools. SonarQube is open-source, java based tool It also needs database as well - Database can be MySQL, Oracle or PostgreSQL.  We will use PostgreSQL as it is open source as well.

Please find steps for installing SonarQube 7.7 on Ubuntu 18.0.4. Make sure port 9000 is opened in security group(firewall rule).


Pre-requistes:
Instance should have at least 2 GB RAM. For AWS, instance should new and type should be t2.small
Make sure port 9000 is opened in security group(firewall rule).
Java 11 installation steps
sudo apt-get update && sudo apt-get install default-jdk -y

2. PostgreSQL Installation
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'

sudo wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add -


sudo apt-get -y install postgresql postgresql-contrib
Ignore the message in red color below:

Start PostGresSQL
sudo systemctl start postgresql
sudo systemctl enable postgresql

Login as postgres user
sudo su - postgres

Now create a sonar user below
createuser sonar

9. Switch to sql shell by entering
psql




Setup user name and password in Postgresql

Execute the below three lines (one by one)
ALTER USER sonar WITH ENCRYPTED password 'password';
CREATE DATABASE sonarqube OWNER sonar;
\q




type exit to come out of postgres user.




3. Now install SonarQube Web App
sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-7.7.zip


sudo apt-get -y install unzip
sudo unzip sonarqube-7.7.zip -d /opt

exi




sudo mv /opt/sonarqube-7.7 /opt/sonarqube -v




Create Group and User:
sudo groupadd sonar

Now add the user with directory access
sudo useradd -c "user to run SonarQube" -d /opt/sonarqube -g sonar sonar 
sudo chown sonar:sonar /opt/sonarqube -R

Modify sonar.properties file
sudo vi /opt/sonarqube/conf/sonar.properties
uncomment the below lines by removing # and add values highlighted yellow
sonar.jdbc.username=sonar
sonar.jdbc.password=password






Next, add the below line:
sonar.jdbc.url=jdbc:postgresql://localhost/sonarqube


Press escape, and enter :wq! to come out of the above screen.

Edit the sonar script file and set RUN_AS_USER
sudo vi /opt/sonarqube/bin/linux-x86-64/sonar.sh
(Scroll down by using down arrow button)
#enable the below line  by removing #
RUN_AS_USER=sonar

 


Create Sonar as a service
Execute the below command:
sudo vi /etc/systemd/system/sonar.service












add the below code in green color:
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

[Install]
WantedBy=multi-user.target

# once you add press :wq! to come out of the above editor
sudo systemctl start sonar

sudo systemctl enable sonar

sudo systemctl status sonar

type q now to come out of this mode.
Now execute the below command to see if Sonarqube is up and running. This may take a few minutes.

(Now Restart EC2 instance by going to AWS console and stop/start the EC2 instance)
Once restarted EC2 instance, login again and check the Sonar logs:

tail -f /opt/sonarqube/logs/sonar.log


Make sure you get the below message that says sonarqube is up..



Now access SonarQube UI by going to browser and enter public dns name with port 9000
Now to go to browser --> http://your_SonarQube_publicdns_name:9000/
Login as admin/admin

Please follow steps for integrating SonarQube with Jenkins

https://www.coachdevops.com/2020/04/how-to-integrate-sonarqube-with-jenkins.html
