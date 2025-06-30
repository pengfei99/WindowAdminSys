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

# update repo cache
sudo apt update

# install
sudo apt install postgresql-16

# set up as system boot
sudo systemctl start postgresql@16-main.service && sudo systemctl enable postgresql@16-main.service

# check client version
psql --version

# check server version
sudo -u postgres psql -c "SELECT version();"
```

## 2. Create db
You need to log in to the `PostgreSQL terminal` with an `admin account`, then run the below commands

```shell
CREATE DATABASE airflow;
```

## 3. Create a User
You need to log in to the `PostgreSQL terminal` with an `admin account`, then run the below commands
```shell
# create user pliu with pwd toto
CREATE USER pliu WITH PASSWORD 'toto';

# change user password
ALTER USER 'user_name' WITH PASSWORD 'new_password';
```

## 4. Connect to the db with the user account

```shell
psql -U <admin-user-name> -d <db-name> -h <host-url>
```