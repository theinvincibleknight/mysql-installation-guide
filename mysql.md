# Installation and Configuration of MySQL on Ubuntu

MySQL being one of the most popular and open-source relational database management systems, you'll find this available with almost every package manager.

Here's the command to update your existing repos and install MySQL in Ubuntu:

```bash
sudo apt update && sudo apt install mysql-server
```
MySQL server which is helpful when you want to host a database server on your system.

Once you are done with the installation, you can check for the installed version by appending `--version` to the `mysql` command:

```bash
mysql --version
```
## Securing MySQL install with a secure script

MySQL is vulnerable just after installation and this is the reason why it is pre-bundled with a secure script.

To start the secure script, utilize the given command and it will ask various questions ranging from profile password to the level of password validation:

```bash
sudo mysql_secure_installation
```

And when I choose my password and security validation level, it gave me the following error:

![mysql-error](https://user-images.githubusercontent.com/30899788/230740973-aa79f71e-3341-4226-b102-321da43e375d.jpg)

## How to solve Failed! Error: SET PASSWORD has no significance for user ‘root@localhost’ as the authentication

The reason why you get this error is that by default, the secure script attempts to set the root password for MySQL install.

And by default, Ubuntu is configured to connect using a password.

But the steps are fairly simple. Let's start with starting MySQL:

```bash
sudo mysql
```

Now, we will set the password to identify `root@localhost`:

```bash
alter user root@localhost identified with mysql_native_password by 'Enter password here';
```

Once done, flush the privileges in MySQL and exit by using the given command:

```
flush privileges;
exit;
```
![root-user-pwd](https://user-images.githubusercontent.com/30899788/230741022-96313471-6e74-4a59-8191-9a2db49f81a6.jpg)

Now, start the secure script again:

```bash
sudo mysql_secure_installation
```

![mysql-error](https://user-images.githubusercontent.com/30899788/230741065-7656db8d-09e2-45b0-a4a8-59328ea1d412.jpg)

That's it. You have successfully secured MySQL!

## Start MySQL on every boot

To check whether MySQL is running or not, you can check its status by using the given command:

```bash
sudo systemctl status mysql
```

And if it's not active, you can start MySQL using the given command:

```bash
sudo systemctl start mysql
```

To start MySQL on every boot, you can use the given command:

```bash
sudo systemctl enable mysql
```

## Connect to MySQL Server

To connect to the MySQL Server, use this command:

```bash
sudo mysql -u root -p
```
It will prompt for the password of the root account. You enter the password and press `Enter`.

Use the `SHOW DATABASES` to display all databases in the current server:

```bash
mysql> show databases;
```
Here is the output:
```
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.05 sec)
```
## Import and Export Databases in MySQL

Importing and exporting databases is a common task in software development. You can use data dumps to back up and restore your information. You can also use them to migrate data to a new server or development environment.

### Exporting a MySQL

The `mysqldump` console utility exports databases to SQL text files. This makes it easier to transfer and move databases. You will need your database’s name and credentials for an account whose privileges allow at least full read-only access to the database.

Use `mysqldump` to export your database:
```bash
mysqldump -u 'username' -p 'database_name' > 'data-dump.sql'
```
- `username` is the username you can log in to the database with
- `database_name` is the name of the database to export
- `data-dump.sql` is the file in the current directory that stores the output.

The command will produce no visual output, but you can inspect the contents of `data-dump.sql` to check if it’s a legitimate SQL dump file.

Run the following command:
```bash
head -n 5 'data-dump.sql'
```
The top of the file should look similar to this, showing a MySQL dump for a database named `database_name`.
```
SQL dump fragment
-- MySQL dump 10.13  Distrib 5.7.16, for Linux (x86_64)
--
-- Host: localhost    Database: 'database_name'
-- ------------------------------------------------------
-- Server version       5.7.16-0ubuntu0.16.04.1
```
If any errors occur during the export process, `mysqldump` will print them to the screen.

### Importing a MySQL

To import an existing dump file into MySQL, you will have to create a new database. This database will hold the imported data.

First, log in to MySQL as **root** or another user with sufficient privileges to create new databases:

```bash
mysql -u root -p
```
This command will bring you into the MySQL shell prompt. Next, create a new database with the following command. In this example, the new database is called `new_database`:

```bash
CREATE DATABASE 'new_database';
```

Then exit the MySQL shell by pressing `CTRL+D`. From the normal command line, you can import the dump file with the following command:
```bash
mysql -u 'username' -p 'new_database' < 'data-dump.sql'
```

If the command runs successfully, it won’t produce any output. If any errors occur during the process, `mysql` will print them to the terminal instead.

To check if the import was successful, log in to the MySQL shell and inspect the data. Selecting the new database with command:
```
mysql> USE new_database;
```
 and then to see tables inside selected database:
 ```
 mysql> SHOW TABLES; 
 ```
You should be able to see some imported database tables here.

## How to uninstall MySQL from Ubuntu
To remove MySQL completely from your system, the first is to stop the MySQL service:

```bash
sudo systemctl stop mysql
```

Now, let's remove every package associated with MySQL so you can have ideal reinstallation:

```bash
sudo apt-get purge --auto-remove mysql-server mysql-client mysql-common mysql-server-core-* mysql-client-core-*
```

And if you want to remove the configuration data:

```bash
sudo rm -rf /etc/mysql /var/lib/mysql
```
Done !!!
