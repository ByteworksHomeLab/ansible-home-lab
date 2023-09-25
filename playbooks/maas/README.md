# First time setup

Connect to the PostgreSQL server and test locally.


```shell
sudo su postgres
psql -U postgres
```[postgresql](..%2Fpostgresql)

Create the MAAS database.

```shell
CREATE DATABASE 'maas_db';
CREATE USER maas WITH ENCRYPTED PASSWORD '<secrete passward>';
GRANT ALL PRIVILEGES ON DATABASE maas_db TO maas;
\q
```

Open up firewall

```shell
sudo ufw allow 5432/tcp
sudo systemctl restart postgresql
exit
```

Sign in from another machine on the network.

```shell
psql -h 193.168.3.3 -d mass_db -U maas
```
