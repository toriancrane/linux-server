# Linux Server (hosting Wild Game Catalog)

# Server details
IP address: `52.40.111.252`

SSH port: `2200`

URL: `http://52.40.111.252`

# Configuration changes

## Add user grader to sudo group
Assuming your Linux distro has a `sudo` group (like Ubuntu 16.04), simply add the user to
this admin group:
```
sudo adduser grader
usermod -aG sudo grader
```

## Update all currently installed packages

`apt-get update` - to update the package indexes

`apt-get upgrade` - to actually upgrade the installed packages

## Set-up SSH keys for user grader

## Disable root login
Change the following line in the file `/etc/ssh/sshd_config`:

From `PermitRootLogin without-password` to `PermitRootLogin no`.

Do `service ssh restart` for the changes to take effect.

## Change timezone to UTC

`sudo timedatectl set-timezone UTC`

## Change SSH port from 22 to 2200
Edit the file `/etc/ssh/sshd_config` and change the line `Port 22` to:

`Port 2200`

Then restart the SSH service:

`sudo service ssh restart`

Grader will now need to use the following command to login to the server:

`ssh grader@35.162.241.191 -p 2200 -i ~/.ssh/linuxCourse`

## Configuration Uncomplicated Firewall (UFW)
By default, block all incoming connections on all ports:

`sudo ufw default deny incoming`

Allow outgoing connection on all ports:

`sudo ufw default allow outgoing`

Allow incoming connection for SSH on port 2200:

`sudo ufw allow 2200/tcp`

Allow incoming connections for HTTP on port 80:

`sudo ufw allow www`

Allow incoming connection for NTP on port 123:

`sudo ufw allow ntp`

To enable the firewall, use:

`sudo ufw enable`

To check the status of the firewall, use:

`sudo ufw status`

## Install Apache to serve a Python mod_wsgi application
Install Apache:

`sudo apt-get install apache2`

Install the `libapache2-mod-wsgi` package:

`sudo apt-get install libapache2-mod-wsgi`

## Installed and configure PostgreSQL
Install PostgreSQL with:

`sudo apt-get install postgresql`

Create a PostgreSQL user called `catalog` with:

`sudo -u postgres createuser -P catalog`

You are prompted for a password. This creates a normal user that can't create
databases, roles (users).

Created a database called `gamecatalog` with:

`sudo -u postgres createdb gamecatalog`

Then ran the following commands to setup the database and pre-fill with information:
```
sudo python database_setup.py
sudo python lotsofgames.py
```

The Ubuntu documentation page on [PostgreSQL][3] was helpful.

## Installed various requirements within a virtual environment
```
sudo apt-get install python-psycopg2 python-flask
sudo apt-get install python-sqlalchemy python-pip
sudo pip install oauth2client
sudo pip install requests
sudo pip install httplib2
sudo pip install Flask
```

An alternative to installing system-wide python modules is to create a virtual
environment for each application using the [virualenv][4] package.

## Installed Git version control software
`sudo apt-get install git`

## Cloned the repository that contains Item Catalog app
Move to the `/WildApp` directory and cloned the repository into the WildApp sub-directory.

## Updated wildapp.wsgi file for this installation
Absolute paths are updated to where the application is located. The application `secret_key` is set to
something random and the PostgreSQL for the `catalog` user is set.

## Updated the Google OAuth client secrets file
Entered the IP address into the Google Developers Console -> API Manager
-> Credentials, in the web client under "Authorized JavaScript origins".


## Configure Apache2 to serve the app
To serve the catalog app using the Apache web server, a virtual host configuration file
needs to be created in the directory `/etc/apache2/sites-available/`, in this case called
`WildApp.conf`.

Documentation for the WSGI parameters can be found [here][5].

