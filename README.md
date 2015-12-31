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
