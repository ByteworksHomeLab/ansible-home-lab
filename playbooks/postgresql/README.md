# HOW TO RUN

Run this command from the root of this project.

```shell
ansible-playbook --ask-become-pass playbooks/postgresql/postgres_install.yml
```

There are some steps not currently handled by this playbook. 

## Set the default postgres password

Sudo into the postgres account.

```shell
sudo -u postgres psql
```

Alter the password.

```sql
ALTER USER postgres PASSWORD 'myPassword';
\q
```

Test the new password.

```shell
 psql -h localhost -U postgres
```

## Check postgresql.conf

```shell
sudo vi /etc/postgresql/14/main/postgresql.conf
```

Make sure it is listening to the host IP

```shell
listen_addresses = '192.168.3.3, localhost'
port = '5432'
```

## Check pb_hba.conf

```shell
sudo vi /etc/postgresql/14/main/pg_hba.conf
```

It should contain these settings:

```text
# PostgreSQL Client Authentication Configuration File
# ===================================================
local all postgres  peer
local all all  md5
host all all 0.0.0.0/0 md5
host all all ::1/128 md5
local replication all  peer
host replication all 0.0.0.0/0 md5
host replication all ::1/128 md5
```

## Open the firewall

```shell
sudo ufw allow 5432 
sudo systemctl restart postgresql
```

# Troubleshooting

```shell
sudo systemctl status postgresql
● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: enabled)
     Active: active (exited) since Sat 2023-09-23 22:10:13 UTC; 3min 45s ago
   Main PID: 25937 (code=exited, status=0/SUCCESS)
        CPU: 4ms

Sep 23 22:10:13 neptune systemd[1]: Starting PostgreSQL RDBMS...
Sep 23 22:10:13 neptune systemd[1]: Finished PostgreSQL RDBMS.


```

```shell
sudo netstat -ltpn
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:5432            0.0.0.0:*               LISTEN      25919/postgres      
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      7751/systemd-resolv 
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      26271/sshd: /usr/sb 
tcp6       0      0 :::5432                 :::*                    LISTEN      25919/postgres      
tcp6       0      0 :::22                   :::*                    LISTEN      26271/sshd: /usr/sb 

```


