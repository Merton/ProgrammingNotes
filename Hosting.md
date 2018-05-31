# System Administrator Tools
### Performance
`top` - The "task manager" for a server, shows all processes and CPU, MEMORY usage. Use `htop` for a more intuitive version. 

`htop` - A more user friendly version of `top` 

`free` - Shows the amount of memory used, free cached etc. Use the `-m` flag to show memory in megabytes.

### IP & Ports, and Network Traffic
`ss -t -a` - Displays all sockets that are currently in use, and being listened on.

`nmap -A localhost` - Scans the whole server checking which ports are being listened too, the network status, apache status and is a good general indication of the status of your TCP/IP connections. This can be done to a remote server by running it on your local machine and replacing `localhost` with the servers IP.

`sudo iptraf` - Shows all the active connections, and requests for the server.

`dstat` - Shows the read/write states of the server

### System Info 
`df` - Displays the disk space and utilisation of the disk on the server. Use the `-h` to display a more human readable format.
If the results show that there is free space, but you are still having storage issues, use the `-hTi` flag to display the inode space - which can become full and cause issues before the physical storage is full.

`du` - Shows the filesizes of every file in a given directory. A good usecase for this is to check the sizes in a particular directory, by adding this: `-sch /var/*` it will display the amount of storage used per directory in the var directory.

### Logs 
`/var/log/` contains the majority of every logging file from different services.

When in this directory, there are some useful and important log files:

`tail syslog` - The system log file, this is useful for seeing what changes have occured and for troubleshooting purposes.

`tail ufw.log` - Will show the Firewalls recent actions, a lot of [UFW BLOCK]'s here indicate that your firewall is working!

`tail fail2ban.log` - Displays fail2bans history of banning potentially malicious IPs

`tail auth.log` - Shows the login attempts (useful for spotting a potential breach)

`tail mysql/error.log` - MySQL log file

`tail apache2/access.log` && `tail apache2/error.log` for apache's access and error logs.


## SSH & Git
### SSH Config
An SSH Config can be used to create alias' and to specify extra ssh params.
Create, or edit it at `~/.ssh/config` (If it does not exist, create it)

```
Host <name-of-host-alias>
  HostName <ip-address-of-host>
  User <your-host-username-here>
  ForwardAgent yes   // Enable SSH permissions to be carried through to server
```

`ssh -T git@bitbucket.org` and `ssh -vT git@bitbucket.org` are useful checks to see if you have the correct permissions, these can be ran either on the server or on your local machine.

### Git Using SSH
You can view the config for a repo by typing `git config -l`
The line `remote.origin.url` indicates the location git will go to to push/pull changes.

This can be set to use ssh, running the git command `git remote set-url origin git@bitbucket.com:USERNAME/REPOSITORY.git`.

You will also need to specify the permissions of the cloned repo to allow the users full access. (Where foo is your user group)
```
chgrp -R foo repodir                 # set the group
chmod -R g+rw repodir                # allow the group to read/write
chmod g+s `find repodir -type d`     # new files get group id of directory
git init --shared=all repodir        # sets some important variables in repodir/config ("core.sharedRepository=2")
```

Mac users will often find that there SSH key gets forgotten on reboot, running `ssh-add -k path/to/privkey` will resolve this.

Also, on the server, the user needs to own the `.ssh` directory, so it is not owned by root.

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
By default Ubuntu installs MySQL v5.7, if incase you need to use a previous version (v5.6) then follow these instructions:

To find all packages installed:
```
dpkg -l | grep mysql
```

This will produce a list like this (Note, this is with MySQL version 5.6 runnning, the versions may differ on your machine): 
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

