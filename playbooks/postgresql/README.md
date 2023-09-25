# HOW TO RUN

Run this command from the root of this project.

```shell
ansible-playbook --ask-become-pass playbooks/postgresql/postgres_install.yml
```

Check the status

```shell
sudo systemctl is-active postgresql terminal
udo systemctl is-enabled postgresql terminal
sudo systemctl status postgresql terminal
sudo pg_isready terminal
```

There are some steps not currently handled by this playbook. 

## Check postgresql.conf

Make sure `/etc/postgresql/14/main/postgresql.conf` is listening to '*'

```shell
listen_addresses = '*'
port = '5432'
```

## Check pb_hba.conf

Check `/etc/postgresql/14/main/pg_hba.conf`. It should contain these settings:

```text

# PostgreSQL Client Authentication Configuration File
# ===================================================
# TYPE  DATABASE    USER      ADDRESS      METHOD
local   all         postgres               peer
local   all         all                    md5
host    all         all        0.0.0.0/0   md5
host    all         all        ::1/128     md5
local   replication all                    peer
host    replication all        0.0.0.0/0   md5
host    replication all        ::1/128     md5
```

Sudo into the postgres account.

```shell
sudo su postgres
psql

CREATE DATABASE maas_db;
CREATE USER maas WITH PASSWORD 'mypass';
GRANT ALL PRIVILEGES ON DATABASE maas_db TO maas;
```
Exit the `postgres` user, then try the new sign in:

```shell
psql -h localhost -U maas -d maas_db
```





