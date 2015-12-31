#### lypgsql94admin
- Installation and Service Configuration
```
yum install postgresql-server
service postgresql-server initdb
systemcel enable postgresql
setsebool -P httpd_can_network_connect_db 1
setenforce 0
sestatus
```
Or,better
```
yum install http://yum.postgresql.org/9.4/redhat/rhel-6-x86_64/pgdg-redhat94-9.4-1.noarch.rpm
yum install postgresql94-server postgresql94-contrib
service postgresql-9.4 initdb
chkconfig postgresql-9.4 on
```

```(tested)
yum install postgresql94-server postgresql94-contrib
/usr/pgsql-9.4/bin/postgresql94-setup initdb
systemctl enable postgresql-9.4.service
systemctl start postgresql-9.4.service
```
- Using the Command Line Client
```
\q
\password postgres
CREATE EXTENSION adminpack;
```
- Creating Databases
```
bash > createdb testdb
psql > \connect testdb
```
Traditional way:
```
psql > CREATE DATABASE testdb2;
show all
DROP DATABASE testdb
or:
bash > dropdb testdb
```
- Creating Tables
```
```
- Roles - Creating and Deleting Users
```
bash > createuser testuser
ALTER USER tesetuser WITH PASSWORD 'pass'
psql -U testuser
cd /var/lib/pgsql/9.4/data/
vi pg_hba.conf
```
change:
```
# "local" is for Unix domain socket connections only
local   all             all                                     password
# IPv4 local connections:
host    all             all             127.0.0.1/32            password
```
Then
```
systemctl restart postgresql-9.4
```
delete user:
```
dropdb
dropuser testuser
```
- Roles - Assigning Permissions
```
GRANT ALL on tbname TO username;
```
- Installing and Configuring myPgAdmin
```
yum install phpPgAdmin httpd
cd /etc/httpd/conf.d
vim phpPgAdmin.conf
```
Edit:
```
#Require local
Require all granted

Allow from all
```
Then
```
systemctl enable httpd
systemctl start httpd
```
```
cd /etc/phpPgAdmin
vim config.inc.php
```
Edit
```
  $conf['servers'][0]['host'] = 'localhost';
  $conf['extra_login_security'] = false;
$conf['owned_only'] = true;
```
And restart:
```
systemctl restart httpd
systemctl restart postgresql-9.4
```
Start by lynx:
```
lynx http://localhost/phpPgAdmin
ifconfig
```
- Accepting External Connections
```
cd /var/lib/pgsql/9.4/data/
vim postgresql.conf
```
edit:
```
listen_addresses='*'
port=5432
```
Then
```
cd /etc/httpd/conf.d
```

- Inserting, Viewing and Deleting Data
- Backing Up and Restoring Databases
```
bash > pg_dump >x.txt
```
Restore:
```
psql dbname < x.txt
```
- Replication - Master Server Configuration
```
cd /var/lib/pgsql/9.4/data/
vim postgresql.conf
```
edit
```
wal_level = hot_standby
max_wal_senders = 1 (no of slaves)
max_keep_segmants = 100
synchronous_standby_names = 'p2'
```
Then create a user for replication(replication is a rule name)
```
create user replica replication
```
And edit pg_hba.conf, add a line
```
host replication replica <target slave ip> trust
```

- Replication - Slave Server Configuration
(in slave machine,)
```
pg_basebackup -D /var/lib/pgsql.9.4/data -h masterip(x.x.x.x) -U replica
vi revovery.conf
```
edit:
```
stand_by=on
trigger_file='/tmp/promotedb'
primary_conninfo='host=<hostip> port=5432 user=replica application_name=<hostname>'
```
Then
```
chown postgres:postgres recovery:conf
```




