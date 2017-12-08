# Linux Server Configuration

### Description

This repository shows how to deploy your Item-Catalog Flask application to a remote server using ssh service from your computer.

### Prerequisites

* I have used AWS(Amazon Web Services) to create a remote server.
* I have used my AWS account to create an instance of EC2 and used the private key provided by AWS to login to the server.
* The OS used on the server is Ubuntu 16.04 .
* The Public IPv4 is used to login using ssh.
* My Public IPv4 is <b>35.154.26.246</b>.

## Instructions

### Login using AWS private key

The following command is used to login to remote server as Administrator:
`$ ssh -i aws_secret_key.pem ubuntu@35.154.26.246`

### Upgrades

Use the following commands to upgrade older packages:
`$ sudo apt update`
`$ sudo apt upgrade`

### Add new user

* Add new user <b>grader</b> `$ sudo adduser grader`. Set its password the then some attributes that are asked.
* Give <b>grader</b> sudo permission, run command:`$ sudo nano /etc/sudoers.d/grader` then add this to the file:`grader ALL=(ALL) NOPASSWD:ALL`.
* Run `$ sudo service ssh restart` to refresh ssh.

### Create new key-pair

We need to create a new RSA key pair for user <b>grader</b>. For this we will use the ssh-keygen tool.
* On you local machine run `$ ssh-keygen`.
* Give the key whatever name you like(eg. `grader`) but it is recommended to store the key in `~/.ssh/` directory.
* There will be two files generated, `~/.ssh/grader` and `~/.ssh/grader.pub`.

### Setting up key for grader account

* Switch to user <b>grader</b> using: `$ su - grader`.
* Create directory to store keys: `$ mkdir .ssh`.
* Create and edit file: `$ nano .ssh/authorized_keys` and add the contents of `~/.ssh/grader.pub` from your local machine to this file.
* Alter permissions for security reasons: `$ sudo chmod 700 .ssh` and `$ sudo chmod 644 .ssh/authorized_keys`.
* Run `$ sudo service ssh restart` to refresh ssh.

Now, you can login in to <b>grader</b> using:
`$ ssh -i ~/.ssh/grader grader@35.154.26.246`

### Configure SSH

* Open ssh configuration file using: `$ sudo nano /etc/ssh/sshd_config`.
* Change default ssh port by changing line:
`Port 22` ---> `Port 2200`
* Disable root login by changing line:
`PermitRootLogin` ---> `PermitRootLogin no`
* Disable password login by changing line:
`PasswordAuthentication yes` ---> `PasswordAuthentication no`
* Save the file, then run `$ sudo service ssh restart` to refresh ssh.

Now, you can login in to <b>grader</b> using:
`$ ssh -i ~/.ssh/grader grader@35.154.26.246 -p 2200`

### Configure UFW

* Install NTP package using: `$ sudo apt install ntp`.
* Run commands:
`$ sudo ufw disable`
`$ sudo ufw default deny incoming`
`$ sudo ufw default deny outgoing`
`$ sudo ufw allow 2200/tcp`
`$ sudo ufw allow www`
`$ sudo ufw allow ntp`
`$ sudo ufw enable`
* The firewall is now configured.

### Install Project Dependencies

* Install deployment dependencies using apt:
`$ sudo apt install apache2 libapache2-mod-wsgi postgresql git python-dev python-pip`
* Install project dependencies using pip:
`$ sudo pip install flask`
`$ sudo pip install httplib2`
`$ sudo pip install sqlalchemy`
`$ sudo pip install requests`
`$ sudo pip install psycopg2`
`$ sudo pip install oauth2client`

### Setup Postgresql

* Login to Postgres using: `$ sudo su - postgres`.
* Run command: `$ psql`.
* Create new database using: `postgres=# CREATE DATABASE catalog;`.
* Create new user using: `postgres=# CREATE USER catalog WITH PASSWORD 'password';`.
* Grant database permissions: `postgres=# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;`.
* Quit Postgres using: `psotgres=# \q` then `$ exit`.

