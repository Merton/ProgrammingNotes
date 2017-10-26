# Craft CMS
## Setting up craft CMS

1. Download repository from Github/Bitbucket etc or the craft files from the craft website if you're starting from scratch.
2. Install & run MAMP,  change the document root in preferences to the public folder inside the craft repo you just cloned ( ie. Documents/mycraftproject/public).
3. Connect to a Database
    - Go to `http://localhost:8888/phpMyAdmin`
    - Click 'new' on the left handside, and name your db.
    - Change the Collation to `utf8_general_ci`
    - Now click on the database, then on the import tab, upload your .sql file (files that are too large will need to be zipped) and click go.
    - Open the db file at Craft/config/db.php and put the following (If you got this from a repository this step can be skipped):
    ```
    'server' => 'localhost',
  	'port' => '8889',
  	'user' => 'your sql username here',
  	'password' => 'your sql username here',
  	'database' => 'the name of the database you created',
    ```
    **NOTE:** MAMP automatically creates a mySQL user called `root` with a password of `root`.
 4. Add localhost to your hosts file at etc/hosts
    ```
      127.0.0.1       localhost                 <--- add this line to your file
      127.0.0.1       coolAliasforWebsite       <--- You can also add alias' for urls to redirect to 127.0.0.1
    ```
 5. Go to `../craft/public` in the terminal and run `npm install`, this will install the all the dependencies for the project.
 6. Enter the command `grunt` into the terminal and it will minify and run the code. 
 
(**Side note**, if you recieve error that the grunt command is not found, run `sudo npm install -g grunt-cli`, this will install the command line interface for grunt. Now retry.)

7. localhost:3000 should have been opened (by grunt) in your browser , and you should see the site!

# Fun Errors
## Caching/Serializer error
Sometimes, deleting the entire cache folder in `Craft/Storage/runtime/` can solve the issue. 

Othertimes it is not an actual issue with caching (or serializers) and instead an issue with the version of PHP you are using. 

First off check what version of PHP you are using (`MAMP -> Preferences -> PHP`).
Craft 2 & Yii is only compatible with PHP <= v7.1 and so either select a lower version, 
or if there is no version low enough you need to change what MAMP displays (As it can only display 2 versions).

Go to `Applications/MAMP/bin/php`, you should see all the versions of PHP that are installed, if you dont see a version less than 7.1, go to the MAMP website to download it. 

Rename the other php folders to .old, apart from the 2 versions you would like to use.

## Cannot connect to database

This can be caused by either not setting the right database access info in the db.php file (`craft/config/db.php`) or, due to mySQL and PHP versioning.

**Potential Solution**

In MAMP, go to `Tools/Check mySQL databases`, and click check.

Pay attention to the following 5 tables:
```
innodb_index_stats
innodb_table_stats
slave_master_info
slave_relay_log_info
slave_worker_info
```
If you see an error stating that the table's do not exist, then do the following:

1. Log into your MySQL server (`Applications/MAMP/Library/bin/mysql -u root -p`), and delete the tables listed above by running: 
```
DROP TABLE innodb_index_stats,
innodb_table_stats,
slave_master_info,
slave_relay_log_info,
slave_worker_info;
```
2. Goto `Applications/MAMP/db/mysqlxx/mysql`, and delete the `*.frm` and `*.ibd` files for the 5 tables above.
3. Now run the following SQL script in MySQL to recreate these tables:
```
CREATE TABLE `innodb_index_stats` (
  `database_name` varchar(64) COLLATE utf8_bin NOT NULL,
  `table_name` varchar(64) COLLATE utf8_bin NOT NULL,
  `index_name` varchar(64) COLLATE utf8_bin NOT NULL,
  `last_update` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  `stat_name` varchar(64) COLLATE utf8_bin NOT NULL,
  `stat_value` bigint(20) unsigned NOT NULL,
  `sample_size` bigint(20) unsigned DEFAULT NULL,
  `stat_description` varchar(1024) COLLATE utf8_bin NOT NULL,
  PRIMARY KEY (`database_name`,`table_name`,`index_name`,`stat_name`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin STATS_PERSISTENT=0;

CREATE TABLE `innodb_table_stats` (
  `database_name` varchar(64) COLLATE utf8_bin NOT NULL,
  `table_name` varchar(64) COLLATE utf8_bin NOT NULL,
  `last_update` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  `n_rows` bigint(20) unsigned NOT NULL,
  `clustered_index_size` bigint(20) unsigned NOT NULL,
  `sum_of_other_index_sizes` bigint(20) unsigned NOT NULL,
  PRIMARY KEY (`database_name`,`table_name`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin STATS_PERSISTENT=0;

CREATE TABLE `slave_master_info` (
  `Number_of_lines` int(10) unsigned NOT NULL COMMENT 'Number of lines in the file.',
  `Master_log_name` text CHARACTER SET utf8 COLLATE utf8_bin NOT NULL COMMENT 'The name of the master binary log currently being read from the master.',
  `Master_log_pos` bigint(20) unsigned NOT NULL COMMENT 'The master log position of the last read event.',
  `Host` char(64) CHARACTER SET utf8 COLLATE utf8_bin NOT NULL DEFAULT '' COMMENT 'The host name of the master.',
  `User_name` text CHARACTER SET utf8 COLLATE utf8_bin COMMENT 'The user name used to connect to the master.',
  `User_password` text CHARACTER SET utf8 COLLATE utf8_bin COMMENT 'The password used to connect to the master.',
  `Port` int(10) unsigned NOT NULL COMMENT 'The network port used to connect to the master.',
  `Connect_retry` int(10) unsigned NOT NULL COMMENT 'The period (in seconds) that the slave will wait before trying to reconnect to the master.',
  `Enabled_ssl` tinyint(1) NOT NULL COMMENT 'Indicates whether the server supports SSL connections.',
  `Ssl_ca` text CHARACTER SET utf8 COLLATE utf8_bin COMMENT 'The file used for the Certificate Authority (CA) certificate.',
  `Ssl_capath` text CHARACTER SET utf8 COLLATE utf8_bin COMMENT 'The path to the Certificate Authority (CA) certificates.',
  `Ssl_cert` text CHARACTER SET utf8 COLLATE utf8_bin COMMENT 'The name of the SSL certificate file.',
  `Ssl_cipher` text CHARACTER SET utf8 COLLATE utf8_bin COMMENT 'The name of the cipher in use for the SSL connection.',
  `Ssl_key` text CHARACTER SET utf8 COLLATE utf8_bin COMMENT 'The name of the SSL key file.',
  `Ssl_verify_server_cert` tinyint(1) NOT NULL COMMENT 'Whether to verify the server certificate.',
  `Heartbeat` float NOT NULL,
  `Bind` text CHARACTER SET utf8 COLLATE utf8_bin COMMENT 'Displays which interface is employed when connecting to the MySQL server',
  `Ignored_server_ids` text CHARACTER SET utf8 COLLATE utf8_bin COMMENT 'The number of server IDs to be ignored, followed by the actual server IDs',
  `Uuid` text CHARACTER SET utf8 COLLATE utf8_bin COMMENT 'The master server uuid.',
  `Retry_count` bigint(20) unsigned NOT NULL COMMENT 'Number of reconnect attempts, to the master, before giving up.',
  `Ssl_crl` text CHARACTER SET utf8 COLLATE utf8_bin COMMENT 'The file used for the Certificate Revocation List (CRL)',
  `Ssl_crlpath` text CHARACTER SET utf8 COLLATE utf8_bin COMMENT 'The path used for Certificate Revocation List (CRL) files',
  `Enabled_auto_position` tinyint(1) NOT NULL COMMENT 'Indicates whether GTIDs will be used to retrieve events from the master.',
  PRIMARY KEY (`Host`,`Port`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 STATS_PERSISTENT=0 COMMENT='Master Information';

CREATE TABLE `slave_relay_log_info` (
  `Number_of_lines` int(10) unsigned NOT NULL COMMENT 'Number of lines in the file or rows in the table. Used to version table definitions.',
  `Relay_log_name` text CHARACTER SET utf8 COLLATE utf8_bin NOT NULL COMMENT 'The name of the current relay log file.',
  `Relay_log_pos` bigint(20) unsigned NOT NULL COMMENT 'The relay log position of the last executed event.',
  `Master_log_name` text CHARACTER SET utf8 COLLATE utf8_bin NOT NULL COMMENT 'The name of the master binary log file from which the events in the relay log file were read.',
  `Master_log_pos` bigint(20) unsigned NOT NULL COMMENT 'The master log position of the last executed event.',
  `Sql_delay` int(11) NOT NULL COMMENT 'The number of seconds that the slave must lag behind the master.',
  `Number_of_workers` int(10) unsigned NOT NULL,
  `Id` int(10) unsigned NOT NULL COMMENT 'Internal Id that uniquely identifies this record.',
  PRIMARY KEY (`Id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 STATS_PERSISTENT=0 COMMENT='Relay Log Information';

CREATE TABLE `slave_worker_info` (
  `Id` int(10) unsigned NOT NULL,
  `Relay_log_name` text CHARACTER SET utf8 COLLATE utf8_bin NOT NULL,
  `Relay_log_pos` bigint(20) unsigned NOT NULL,
  `Master_log_name` text CHARACTER SET utf8 COLLATE utf8_bin NOT NULL,
  `Master_log_pos` bigint(20) unsigned NOT NULL,
  `Checkpoint_relay_log_name` text CHARACTER SET utf8 COLLATE utf8_bin NOT NULL,
  `Checkpoint_relay_log_pos` bigint(20) unsigned NOT NULL,
  `Checkpoint_master_log_name` text CHARACTER SET utf8 COLLATE utf8_bin NOT NULL,
  `Checkpoint_master_log_pos` bigint(20) unsigned NOT NULL,
  `Checkpoint_seqno` int(10) unsigned NOT NULL,
  `Checkpoint_group_size` int(10) unsigned NOT NULL,
  `Checkpoint_group_bitmap` blob NOT NULL,
  PRIMARY KEY (`Id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 STATS_PERSISTENT=0 COMMENT='Worker Information';
```
4. Restart MySQL server.
5. Go MAMP -> Tools/Upgrade MySQL databases
