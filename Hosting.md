# Ubuntu Server setup
### LAMP Stack

Linux: Ubuntu v16.04.3 LTS
Apache: v2.4.29
MySQL: v5.6
PHP: v5.6

a2dismod [service]
a2enmod [service]
a2ensite [site]
a3dissite [site]


## Downgrade MySQL Version

To find all packages installed:
```
dpkg -l | grep mysql
```

This will produce a list like this: 
```
ii  libdbd-mysql-perl             4.033-1ubuntu0.1                           amd64        Perl5 database interface to the MySQL database
ii  libmysqlclient20:amd64        5.7.21-0ubuntu0.16.04.1                    amd64        MySQL database client library
ii  mysql-client-5.6              5.6.16-1~exp1                              amd64        MySQL database client binaries
ii  mysql-client-core-5.6         5.6.16-1~exp1                              amd64        MySQL database core client binaries
ii  mysql-common                  5.7.21-0ubuntu0.16.04.1                    all          MySQL database common files, e.g. /etc/mysql/my.cnf
ii  mysql-server-5.6              5.6.16-1~exp1                              amd64        MySQL database server binaries and system database setup
ii  mysql-server-core-5.6         5.6.16-1~exp1                              amd64        MySQL database server binaries
ii  php5.6-mysql                  5.6.33-3+ubuntu16.04.1+deb.sury.org+1      amd64        MySQL module for PHP
```

Systematically remove every package of the mysql package:
`sudo dpkg --purge <package_name>`

Then, run these to ensure that the packages have been removed:
```
sudo apt-get --purge remove mysql-server
sudo apt-get --purge remove mysql-client
sudo apt-get --purge remove mysql-common
sudo apt-get autoremove
sudo apt-get autoclean
```

Remove MySQL data: `sudo rm -rf /etc/mysql`

Remove MySQL library`sudo rm -rf /var/lib/mysql/`

Use `which mysql` & `mysql --version`

Now you can install the MySQL version of your choice, to install older versions you will need to add the archive repository: 

```
sudo add-apt-repository 'deb http://archive.ubuntu.com/ubuntu trusty universe'
```
Then install the MySQL packages:
```
sudo apt-get update
sudo apt install mysql-server-5.6
sudo apt install mysql-client-5.6
```

