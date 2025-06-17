# Install mariadb on windows

In this tutorial, we will install a mariadb without admin rights

## 1. Download the ZIP Archive
You can download the bin from this website: https://mariadb.org/download/

As we don't have the admin rights, so we don't want the installer.

Choose:
- OS: Windows
- Package: ZIP archive (not MSI installer)
- Architecture: x64 or x86 based on your system

```shell
Extract the ZIP file to a folder you control, e.g.,
C:\Users\path\to\MariaDB

```

## 2. Configure MariaDB


Create a `my.ini` file in the root directory of the extracted folder:

Below is an example, you need to change the path
```ini
[mysqld]
basedir=C:\Users\PLIU\Documents\Tool\DB\mariadb-11.8.2-winx64
datadir=C:\Users\PLIU\Documents\Tool\DB\mariadb-11.8.2-winx64\data
port=3307
socket=mysql.sock
character-set-server=utf8mb4
collation-server=utf8mb4_general_ci
sql_mode=STRICT_TRANS_TABLES
```

### 2.1 Initialize the Database

Open Command Prompt in the MariaDB folder:

```shell
# go to the bin folder of mariadb
cd C:\Users\PLIU\Documents\Tool\DB\mariadb-11.8.2-winx64\bin

# run the below command to initialize the database
mysql_install_db.exe

# expected output
Default data directory is C:\Users\PLIU\Documents\Tool\DB\mariadb-11.8.2-winx64\data
Running bootstrap
Creating my.ini file
Removing default user
Creation of the database was successful
```

> The folder C:\Users\PLIU\Documents\Tool\DB\mariadb-11.8.2-winx64\data must exist and empty. Otherwise, the command will fail

## 3. Start MariaDB

Run in Command Prompt:

```shell
# start the db server in interactive mode, close the cmd will stop the server
# all logs will show in the terminal
mysqld.exe --console

# start the db as background process
mysqld.exe --standalone

# expected output
Version: '11.8.2-MariaDB'  socket: ''  port: 3307  mariadb.org binary distribution
```

This keeps the MariaDB server running in your console session.

> This is recommended for development usage.

## 4. Connect to MariaDB

Open a new Command Prompt window:

```shell
mariadb.exe -u root -P 3307

# show database
show databases;

#  Create the database
CREATE DATABASE recette_constance CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;

# Create a new user and set the password
CREATE USER 'casd'@'localhost' IDENTIFIED BY 'toto';

# Grant all privileges on the new database to the new user
GRANT ALL PRIVILEGES ON recette_constance.* TO 'casd'@'localhost';

# Apply the changes
FLUSH PRIVILEGES;


# test the connexion
mariadb.exe -u casd -p -h 127.0.0.1 -P 3307
```

> If you use another port, you need to change it.

## 5. Add to PATH Temporarily (Optional)

```shell
set PATH=%PATH%;C:\Users\xxxxxxxx\MariaDB\bin

```

## 6. Shut Down MariaDB

```shell
bin\mariadb-admin.exe -u root shutdown
```
