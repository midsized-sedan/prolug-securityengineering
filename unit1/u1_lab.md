# Unit 1 Lab

## Module 1: Exploring System Information

Exercise 1.1: Familiarizing ourselves with the System

```
mount command by itself is the same as mount -l which lists all mounts


mount | grep -i noexec
about 15

mount | grep -i nodev
about 17

mount | grep -i nosuid
about 19

# Approximately how many of your mounted filesystems have each of these values?
```


Exercise 1.2: Checking Mounted Systems

```
sysctl -a | grep -i ipv4
315
sysctl -a | grep -i ipv6
296
# How many of each are there?
```

```
sysctl -a | grep -i ipv4 | grep -i forward

# Does IPv4 forward on interfaces?
all the settings are 0 except net.ipv4.ip_forward_update_priority = 1

No ipv4 does not forward on interfaces
```

```
lsmod | grep -i tables
# What type of tables exist?
nf_tables exist

nftables is a net filter
```

## Module 2: PreLAB

Download STIG Viewer and STIG for MariaDB

Done.


## Module 3: Lab

```
dnf install mariadb-server

# Ensure that it's running

systemctl start mariadb

system status mariadb

ss -ntulp | grep 3306
tcp   LISTEN 0      80                 *:3306             *:*    users:(("mariadbd",pid=23324,fd=19))
```

### Check and remediate v-253666 STIG

Rule Title: MariaDB must limit the number of concurrent sessions to an 
organization-defined number per user for all accounts and/or account types.

What is the problem?
  - Allowing unlimited connections could cause the system to be overloaded and fail.
  This can also allow Denial of Service attacks.

What is the fix?
  - Limit the number of allowed concurrent connections.

What type of control is being implemented?
  - Technical Preventative

Is it set properly on your system?
  - No

Connect to MariaDB locally

```
mysql
```

Run the SQL command in the STIG Fix Text Section:

```
SELECT user, max_user_connections FROM mysql.user;


I had to use this one
select user, host, max_user_connections from mysql.user;
```

Can you remediate this finding?

```
GRANT USAGE ON *.* TO 'mariadb.sys'@'localhost' WITH MAX_USER_CONNECTIONS 2;
GRANT USAGE ON *.* TO 'root'@'localhost' WITH MAX_USER_CONNECTIONS 2;
GRANT USAGE ON *.* TO 'mysql'@'localhost' WITH MAX_USER_CONNECTIONS 2;

MariaDB [(none)]> select user, host, max_user_connections from mysql.user;
+-------------+-----------+----------------------+
| User        | Host      | max_user_connections |
+-------------+-----------+----------------------+
| mariadb.sys | localhost |                    2 |
| root        | localhost |                    2 |
| mysql       | localhost |                    2 |
+-------------+-----------+----------------------+
3 rows in set (0.002 sec)

```


### Check and remediate v-253677 STIG

Rule Title
  - MariaDB must by default shut down upon audit failure, 
  to include the unavailability of space for more audit log records; 
  or must be configurable to shut down upon audit failure.

What is the problem?
  - If there is ever an issue that interrups audit logging, then there may be a period
  where logs are not recording, leaving the possiblity for access and/or changes
  being made that cannot be tracked

What is the fix?
  - The fix is to have mariadb shut down immediately once there is an issue
  with audit logs.

What type of control is being implemented?
  - Technical Preventative

Is it set properly on your system?
  - No. MariaDB does not have any auditing enabled by default, and its a fresh installation.

Remediate

Logrotate handles daily rotation of logs.
System availability is more important than audit trail.




### Check and remediate v-253678 STIG

Rule Title
  - MariaDB must be configurable to overwrite audit log records, oldest first (First-In-First-Out - FIFO),
  in the event of unavailability of space for more audit log records.

What is the problem?
  - There always needs to be an audit trail, so if MariaDB runs out of space to
  record logs, the auditing process can fail.

What is the fix?
  - The fix is to allow for logs to be overwritten in a FIFO fashion, where
  the oldest logs are overwritten first.

What type of control is being implemented?
  - Technical Preventative

Is it set properly on your system?
  - No, as there is no logging currently for my mariadb.

Remediate

(MariaDB Docs on Rotating)[https://mariadb.com/kb/en/rotating-logs-on-unix-and-linux/]

**First setup logs**

Set options in /etc/my.cnf
```
[mysqld]
log_error=/var/log/mysql/mariadb.err
general_log
general_log_file=/var/log/mysql/mariadb.log
slow_query_log
slow_query_log_file=/var/log/mysql/mariadb-slow.log
long_query_time=5
```

Create relavant directory
```
sudo mkdir /var/log/mysql/
sudo chown mysql:mysql /var/log/mysql/
sudo chmod 0770 /var/log/mysql/
```

**Setup logrotate**

Configure root@localhost user account to allow logrotate utility to authenticate
```
ALTER USER 'root'@'localhost' IDENTIFIED VIA unix_socket;
```

setup /etc/logrotate.d/mariadb file
```
/var/log/mysql/* {
        su mysql mysql
        missingok
        create 660 mysql mysql
        notifempty
        daily
        minsize 1M
        maxsize 100M
        rotate 30
        dateext
        dateformat .%Y-%m-%d-%H-%M-%S
        compress
        delaycompress
        sharedscripts
        olddir archive/
        createolddir 770 mysql mysql
    postrotate
        if test -x /usr/bin/mysqladmin && \
           /usr/bin/mysqladmin ping &>/dev/null
        then
           /usr/bin/mysqladmin --local flush-error-log \
              flush-engine-log flush-general-log flush-slow-log
        fi
    endscript
}
```

**Logrotate to regularly rotate logs**

logrotate.timer will run logrotate daily at midnight.

```
[root@hammer7 etc]# systemctl status logrotate.timer
● logrotate.timer - Daily rotation of log files
     Loaded: loaded (/usr/lib/systemd/system/logrotate.timer; enabled; preset: enabled)
     Active: active (waiting) since Fri 2025-04-11 14:25:28 MST; 2h 2min ago
      Until: Fri 2025-04-11 14:25:28 MST; 2h 2min ago
    Trigger: Sat 2025-04-12 00:00:00 MST; 7h left
   Triggers: ● logrotate.service
       Docs: man:logrotate(8)
             man:logrotate.conf(5)

Apr 11 14:25:28 hammer7 systemd[1]: Started Daily rotation of log files.
```

### Check and remediate v-253734 STIG

Rule Title
  - MariaDB must disable network functions, ports, protocols, and services deemed 
  by the organization to be nonsecure, in accord with the Ports, Protocols, 
  and Services Management (PPSM) guidance.

What is the problem?
  - If Mariadb allows network functionality with nonsecure ports, protocols, and 
  services management, it can expose the DB to unauthorized access.

What is the fix?
  - Check the currently defined port configuration. If it is not correct,
  locate the cnf file and specifically bind ip address to deny (or port).
  Also change the default port of 3306 to something different.
  After making the changes, stop and restart the service.

What type of control is being implemented?
  - Technical Preventative

Is it set properly on your system?
  - No

Remediate

The remediation only works if mariadb is intended to be used locally.
If meant to be connected to outside, you will need to setup firewall access rules.

edit the /etc/my.cnf file, adding the following
```
[mysqld]
port = 3300
bind-address = 127.0.0.1
```

Restart the service
```
systemctl restart mariadb.service
```

Check to make sure the port has changed

```
[root@hammer20 ~]# ss -tulnp | grep maria
tcp   LISTEN 0      80         127.0.0.1:3300       0.0.0.0:*    users:(("mariadbd",pid=32484,fd=19))


or

MariaDB [(none)]> SHOW GLOBAL VARIABLES LIKE 'port';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| port          | 3300  |
+---------------+-------+
1 row in set (0.002 sec)

MariaDB [(none)]>

```

