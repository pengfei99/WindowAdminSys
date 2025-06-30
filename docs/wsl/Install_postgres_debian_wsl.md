# Install postgresql in debian 11 under wsl2

In this tutorial, we will show how to install postgres in debian 11 under wsl2

## 1. Install postgres 

### 1.1 Via system default package
```shell
# install the package
sudo apt update
# the below command will install:
# - PostgreSQL server
# - Common client tools (psql)
# - Contrib modules like pg_stat_statements, uuid-ossp, etc
sudo apt install postgresql postgresql-contrib -y

# check psql version
psql --version

# start the postgresql service
sudo service postgresql start

# try to login as postgres(default admin account)
sudo -u postgres psql

# list database
\l 

# quit the terminal
\q

```

> For debian 11, the default postrgres version is 13.21(too old). We want a newer version such as 16.
> 

### 1.2 Use a custom repo package

#### 1.2.1 Remove all existing postgres versions

```shell

sudo apt purge postgresql* -y

# remove all dependencies
sudo apt autoremove --purge -y

# clean conf and data
sudo rm -rf /etc/postgresql/
sudo rm -rf /var/lib/postgresql/
sudo rm -rf /var/log/postgresql/
sudo rm -rf /var/run/postgresql/

# check the result
which psql

# remove user and group
sudo deluser postgres
sudo groupdel postgres
```

#### 1.2.2 Add official postgres repo

```shell
# add required packages
sudo apt install vim gnupg2 -y

# add gpg key
curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg

# add repo to source list
sudo sh -c 'echo "deb https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'

# you may need to install  lsb-release if the above command does not work
sudo apt install lsb-release -y

# update repo cache
sudo apt update

# install
sudo apt install postgresql-16

# set up as system boot, you need to enable systemd in wsl first.
sudo systemctl start postgresql@16-main.service && sudo systemctl enable postgresql@16-main.service

# check status
sudo systemctl status postgresql@16-main.service

# check client version
psql --version
# expected output
psql (PostgreSQL) 16.9 (Debian 16.9-1.pgdg110+1)

# check server version
sudo -u postgres psql -c "SELECT version();"
# expected output
-----------------------------------------------------------------------------------------------------------------------------
 PostgreSQL 16.9 (Debian 16.9-1.pgdg110+1) on x86_64-pc-linux-gnu, compiled by gcc (Debian 10.2.1-6) 10.2.1 20210110, 64-bit
(1 row)
```

### 1.3 configure postgres

There are two main conf you need to modify to allow remote access:
- **pg_hba.conf**: default location: `/etc/postgresql/16/main/pg_hba.conf`
- **postgresql.conf**: default location `/etc/postgresql/16/main/postgresql.conf`

```shell
# allow remote access
sudo vim /etc/postgresql/16/main/postgresql.conf

# find the below line, set the default value localhost to *
listen_addresses = '*' 
```

```shell
sudo vim /etc/postgresql/16/main/pg_hba.conf

# allow remote password connection
host    all             all             0.0.0.0/0           md5
```


## 2. Create db
You need to log in to the `PostgreSQL terminal` with an `admin account`, then run the below commands

```shell
# login to the PostgreSQL terminal with default admin
sudo -u postgres psql

# create db
CREATE DATABASE airflow;
```

## 3. Create a User
You need to log in to the `PostgreSQL terminal` with an `admin account`, then run the below commands
```shell
# create user pliu with pwd toto
CREATE USER <user_name> WITH PASSWORD <pwd>;

# change user password
ALTER USER 'user_name' WITH PASSWORD 'new_password';

# grant user access to db 
GRANT ALL PRIVILEGES ON DATABASE <db_name> TO <user_name>;
```

## 4. Connect to the db with the user account

```shell
psql -U <user-name> -d <db-name> -h <host-url>

# for example login to db airflow with user airflow
psql -U airflow -d airflow -h localhost
```