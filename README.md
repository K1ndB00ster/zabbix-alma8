<!DOCTYPE html>
<html>
<head>
</head>
<body>
<p {
  style="color:cyan;"
}>U can use this script by yur's responsibility, but it must work</p>
<img src=https://img.shields.io/badge/shell_script-%23121011.svg?style=for-the-badge&logo=gnu-bash&logoColor=white> <img src=https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54>
 <img src=https://img.shields.io/badge/cent%20os-002260?style=for-the-badge&logo=centos&logoColor=F0F0F0> <img src=https://img.shields.io/badge/-Rocky%20Linux-%2310B981?style=for-the-badge&logo=rockylinux&logoColor=white> <img src=https://img.shields.io/badge/Red%20Hat-EE0000?style=for-the-badge&logo=redhat&logoColor=white>
  
</body>
</html>
<h6>Sorce code:</h6>

 ```console
#!/bin/sh
sudo yum -y install python3
sudo yum -y install nano
sudo yum -y install dnf
sudo yum -y install net-tools
sudo yum -y install iptables-services
sudo yum -y install wget
sudo yum -y install pip
sudo dnf install curl -y
pip3 install mysql-connector-python==8.0.29
pip3 install pymysql
pip3 install click
cd /home
sudo touch zabbix-installer.py
sudo chmod +x zabbix-installer.py
echo '''import os
import pymysql
import click
import getpass

os.system('sudo dnf upgrade --refresh -y')
os.system('''curl -LsS https://downloads.mariadb.com/MariaDB/mariadb_repo_setup\
 | sudo bash -s -- --mariadb-server-version=10.8''')
os.system('sudo dnf install mariadb-server mariadb')
os.system('sudo systemctl enable mariadb --now')
click.pause('Press enter for start setup DB!')
os.system('sudo mariadb-secure-installation')

os.system('rpm -Uvh https://repo.zabbix.com/zabbix/5.0/rhel/8/x86_64/zabbix-release-5.0-1.el8.noarch.rpm')
os.system('dnf clean all')
os.system('dnf install zabbix-server-mysql zabbix-web-mysql zabbix-apache-conf zabbix-agent')

password2 = getpass.getpass('Enter password for your zabbix DB: ')
Region = input('Enter your region, for example: Europe/London: ')
mydb = pymysql.connect(
  host="localhost",
  user="root",
  passwd=f"{password2}"
)

mycursor = mydb.cursor()

mycursor.execute("create database zabbix character set utf8 collate utf8_bin;")
mycursor.execute(f"create user zabbix@localhost identified by '{password2}';")
mycursor.execute("grant all privileges on zabbix.* to zabbix@localhost;")
mycursor.close()
os.system(f'''zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p zabbix''')
with open('/etc/zabbix/zabbix_server.conf', 'r') as f:
    old_data = f.read()

new_data = old_data.replace(f'# DBPassword=', f'DBPassword={password2}')

with open('/etc/zabbix/zabbix_server.conf', 'w') as f:
    f.write(new_data)
with open('/etc/zabbix/zabbix_server.conf', 'r') as f:
    old_data = f.read()

new_data = old_data.replace(f'# DBHost=localhost', f'DBHost=localhost')

with open('/etc/zabbix/zabbix_server.conf', 'w') as f:
    f.write(new_data)

with open('/etc/php-fpm.d/zabbix.conf', 'r') as f:
    old_time_data = f.read()

new_time_data = old_time_data.replace(f'''; php_value[date.timezone] = Europe/Riga''', f'''php_value[date.timezone] = \
{Region}''')
with open('/etc/php-fpm.d/zabbix.conf', 'w') as f:
    f.write(new_time_data)
os.system('systemctl restart zabbix-server zabbix-agent httpd php-fpm')
os.system('systemctl enable zabbix-server zabbix-agent httpd php-fpm')
os.system('''sudo yum update httpd && sudo yum install httpd\
 && sudo firewall-cmd --permanent --add-service=http && \
 sudo firewall-cmd --permanent --add-service=https''')
os.system('''sudo firewall-cmd --permanent --add-service=zabbix-agent\
 && sudo firewall-cmd --permanent --add-service=zabbix-server\
 && sudo firewall-cmd --reload\
 && sudo systemctl start httpd''')
os.system('''sudo yum -y install dnf && sudo dnf install firewalld\
 && sudo systemctl enable firewalld && sudo systemctl start firewalld && sudo firewall-cmd --state''')
os.system('systemctl restart firewalld')

with open('/etc/selinux/config', 'r') as f:
    old_data = f.read()

new_data = old_data.replace(f'SELINUX=enforcing', f'SELINUX=disabled')

with open('/etc/selinux/config', 'w') as f:
    f.write(new_data)

with open('/etc/httpd/conf/httpd.conf', 'r') as f:
    old_data = f.read()

new_data = old_data.replace(f'DocumentRoot "/var/www/html"', f'DocumentRoot "/usr/share/zabbix"')

with open('/etc/httpd/conf/httpd.conf', 'w') as f:
    f.write(new_data)

with open('/etc/firewalld/firewalld.conf', 'r') as f:
    old_data = f.read()

new_data = old_data.replace(f'AllowZoneDrifting=yes', f'AllowZoneDrifting=no')

with open('/etc/firewalld/firewalld.conf', 'w') as f:
    f.write(new_data)


os.system('sudo dnf -y install mysql-devel redhat-rpm-config gcc')
os.system('sudo systemctl start firewalld')
os.system('sudo systemctl enable firewalld')
os.system('sudo firewall-cmd --permanent --add-service=snmp')
os.system('sudo firewall-cmd --permanent --add-service=syslog')
os.system('sudo firewall-cmd --permanent --add-service=mysql')
os.system('sudo systemctl enable --now zabbix-agent')
os.system('sudo systemctl enable --now zabbix-server')
os.system('cd /home && rm -f zabbix-installer.py zabbix-installer.sh')
''' > zabbix-installer.py

python3 zabbix-installer.py
```
