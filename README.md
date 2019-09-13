# Linux-Server-Configuration


## Project Description

In this project I took a base-line server and configured it to be able to host my catalog application project, Oracle Books. I used a Ubuntu Linux virtual machine that is hosted on [Amazon Lightsail](https://lightsail.aws.amazon.com).


## Dependencies/Pre-requisites

 1. [Amazon Lightsail](https://lightsail.aws.amazon.com)
     * A Linux Ubuntu 16.04 Virtual Machine

 2. [SQLAlchemy](https://www.sqlalchemy.org/)

 3. [Apache2](https://httpd.apache.org/)

 4. [Flask](https://palletsprojects.com/p/flask/)

 5. [Mod_Wsgi](https://modwsgi.readthedocs.io/en/develop/)

 6. [PostgreSQL](https://www.postgresql.org/)

 7. [Psycopg2](https://pypi.org/project/psycopg2/)

 8. [Git](https://git-scm.com/)

 9. [Virtualenv](https://virtualenv.pypa.io/en/latest/)

 10. [Httplib2](https://pypi.org/project/httplib2/)

 11. [Python Requests](https://2.python-requests.org/en/master/)

 12. [Oauth2client](https://pypi.org/project/oauth2client/)

 13. [Libpq-dev](https://pypi.org/project/libpq-dev/)

 3. [Jinja Template Engine](https://palletsprojects.com/p/jinja/)

 4. HTML

 5. [Bootstrap](https://getbootstrap.com/)

 6. [Google OAuth](https://developers.google.com/identity/sign-in/web/)


## Connection Details

  * IP address: 35.171.139.121

  * Accessible SSH Port: 2200

  * Application URL:<http://35.171.139.121.xip.io>


## Setup/Installation

### Step 1: Create an Instance with Amazon Lightsail

  1. Create a [Amazon Lightsail](https://lightsail.aws.amazon.com) Account

  2. Use the Amazon Lightsail console to create Ubuntu Instance.

  3. Choose a payment plan.

  4. Give the instance a unique name.

  5. Wait for the instance to start up.


### Step 2: Connect to the Instance on  a Local Machine

  1. Go to the Amazon Lightsail page and to download the instance's private key.

  2. Download the private key by clicking on "Download Default Key".

  3. A File either called `LightsailDefaultPrivateKey.pem` or `LightsailDefaultPrivateKey-YOUR-AWS-REGION.pem` will be downloaded. Open this file in s text editor.

  4. Copy the text of the private key into a file called `lightsail_key.rsa`

  5. Connect to the instance by running `ssh -i lightsail_key.rsa ubuntu@35.171.139.121`



### Step 3: Update all currently installed packages.

  1. Run `sudo apt-get update` to see a list of packages available to be upgrade.

  2. Run `sudo apt-get upgrade` to upgrade the packages.


### Step 4: Changing the SSH Port

###Change the SSH port from `22` to `2200`. Open up and edit the `sshd_config`.

  * Open up the `/etc/ssh/sshd_config` file and change the port number from `22` to `2200` using the nano text editor.



### Step 5: Set up a Basic UFW Firewall

  1. Check the current UFW rule set by running `sudo ufw status` or `sudo status verbose`.

  2. Run `sudo ufw default deny incoming` to set the ufw firewall to block all incoming traffic by traffic.

  3. Run `sudo ufw default allow outgoing` to set the ufw firewall to allow all outgoing traffic by default.

  4. Run `sudo ufw allow 2200` To allow an SSH connect on port 2200.

  5. Run `sudo ufw allow 80` to allow an HTTP connection on port 80.

  6. Run `sudo ufw allow 123` to all an NTP connection on port 123.

  7. Run `sudo ufw allow 443` to allow an HTTPS connection on port 443.

  8. Run `sudo ufw enable` to activate the UFW firewall settings.

  9. Run `sudo ufw status verbose` to view the UFW firewall settings.

  ```
  Status: active
  Logging: on (low)
  Default: deny (incoming), allow (outgoing), disabled (routed)
  New profiles: skip

  To                         Action      From
  --                         ------      ----
  2200                       ALLOW IN    Anywhere                  
  80                         ALLOW IN    Anywhere                  
  443                        ALLOW IN    Anywhere                  
  123                        ALLOW IN    Anywhere                  
  2200 (v6)                  ALLOW IN    Anywhere (v6)             
  80 (v6)                    ALLOW IN    Anywhere (v6)             
  443 (v6)                   ALLOW IN    Anywhere (v6)             
  123 (v6)                   ALLOW IN    Anywhere (v6)  
  ```
  10. Update the external firewall of Amazon lightsail inside the browser to match the SSH settings that you used for the ufw firewall.

     * These settings can be access by clicking on the "Networking" tab inside on the console of your Amazon Lightsail instance.

  11. Restart the SSH by running `sudo service ssh restart`.

  12. Connect to the instance by running `ssh -i lightsail_key.rsa -p 2200 ubuntu@35.171.139.121`. Please note that once you change the SSH port from `22` to `2200` you will no longer be able to access the instance using the browser based terminal, you will have to connect from your local terminal.


### Step 6: Give Grader Access to the Instance

  1. Create the user grader by running `sudo adduser grader`

  2. After adding the user your will be asked a few questions starting with a password.

      * Enter a password and any additional information that you would like. You are not requires to answer any of the questions other than the password prompt, and you can hit `RETURN` for any question that you would like to skip.

  3. Give the user `sudo` permissions.

      1. Run `usermod -aG sudo grader`

      2. Run `sudo -s` to log in to the `root` account and then run `visudo` which will open the `/etc/sudoers` file for editing.

          * To add all administrative privileges scroll down to the bottom of the file and add
           `grader  ALL=(ALL:ALL) ALL`.

          * To verfiy grader has `sudo` permissions run `su - grader` enter the password for grader run `sudo -l`. If grader has `sudo` permissions, tou should get the following output:

```
          Matching Defaults entries for grader on
              ip-172-26-1-133.ec2.internal:
              env_reset, mail_badpass,
              secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

          User grader may run the following commands on
              ip-172-26-1-133.ec2.internal:
              (ALL : ALL) ALL
              (ALL : ALL) ALL
              (ALL : ALL) ALL
```

### Step 7: Create an SSH Key for the `grader` user.

  1. Run `ssh-keygen` to generate a SSH key on your local machine.

  2. Chose a file name such as `grader_key`.

  3. Enter a passphase twice or hit `RETURN` or `ENTER` twice to leave the passphase blank. Two files will be generated, the second will enter in `.pub`.

  4. Log into the virtual machine.

  5. Switch to the `grader` home directory and run `mkdir .ssh` to create a directory called `.ssh`.

  6. `cd` into the newly created `.ssh` directory and run`touch authorized_keys` to create a file called `authorized_keys`.

  7. In the local terminal, run `cat ~/.ssh/file_name.pub`

  8. Run `sudo nano authorized_keys` to edit the file on the virtual machine.

  9. Paste the contents of the `.pub` file in the local terminal in to the contents to the authorized_keys file on the virtual machine.

  10. Force key-based authentication and disable password loging for `grader`.

    * Login as `grader`

    * Open the `/etc/ssh/sshd_config` file.

    * Locate the line that says:"PasswordAuthentication: yes" and change "yes" to "no", save and exit the file.

  11. Run `sudo service ssh restart`

  12. Log in to `grader` by using the following command:

      `ssh -i ~/.ssh/grader_key -p 2200 grader@35.171.139.121`


### Step 8: Configure the Local Time Zone to UTC

   1. Run `sudo dpkg-reconfigure tzdata`

   2. Scroll to the bottom of the Continents list and select `Etc` or `None of the above`

   3. In the second list select `UTC`


### Step 9: Install Apache

  1. Run `sudo apt-get update`

  2. Run `sudo apt-get apache2`

  3. Once Apache is installed direct your browser to your server's IP address. In this project the IP address of the server is:`54.234.10.173`. If Apache is working you should be directed to the  the Apache default page: "Apache2 Ubuntu Default Page".


### Step 10: Install and Enable `mod_wsgi`

  1. WSGI(Web Server Gateway Interface) is an interface between web servers and web apps for Python. `Mod_wgsi` is an Apache HTTP server mod that enables Apache to serve flask.

  2. Since the project being deployed on this server was built with Python 3 the Python 3 `mod_wsgi` package needs to be installed on this server To install the package run `sudo apt-get install libapache2-mod-wsgi python-dev`.

  3. Enable `mod_wsgi` by running `sudo a2enmod wsgi`


### Step 11: Install and Configure PostgreSQL

  1. Install PostgreSQL by running `apt-get install postgresql`.

  2. Don't allow remote connections.

  3. Create a new database user named `catalog` that has limited permissions to your catalog application database.

  4. Switch to the `postgres` user on your server by running `sudo -i -u postgres`.

  5. Access the Postgres prompt by running `psql`.

  6. Create the user `catalog` by running `CREATE ROLE catalog WITH LOGIN`.

  7. Give the user the permission to create a database by running `ALTER ROLE catalog CREATEDB;`.

  8. Create a password for the `catalog` user by running `\password catalog` and enter a password when prompted.

  9. Run `\du` to confirm that the `catalog` user was created with the proper permissions. You should see the following input:

      ```
                                        List of roles
      Role name |                         Attributes                         | Member of
      -----------+------------------------------------------------------------+-----------
      catalog   | Create DB                                                  | {}
      postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS | {}
      ```

  10. Exit `psql` by running `\q`.

  11. Switch back to the `ubuntu` user by running `exit`.


### Step 12: Create a Linux User Called `catalog`

  1. Create a new user called `catalog` bt running `sudo adduser catalog`.

  2. Enter a UNIX password twice and fill out the user information for the `catalog` user.

  3. Give the `catalog` user `sudo` permissions.

  4. Run `sudo -s` to log in to the `root` account and then run `visudo` which will open the `/etc/sudoers` file for editing.

      * To add all administrative privileges scroll down to the bottom of the file and add
           `catalog  ALL=(ALL:ALL) ALL`.

        * To verfiy grader has `sudo` permissions run `su - catalog` enter the password for grader run `sudo -l`. If grader has `sudo` permissions, tou should get the following output:


```
              Matching Defaults entries for catalog on
                  ip-172-26-13-24.ec2.internal:
                  env_reset, mail_badpass,
                  secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

            User catalog may run the following commands on
                  ip-172-26-13-24.ec2.internal:
                  (ALL : ALL) ALL
```

### Step 13: Create a New Database Named `catalog`

  1. Log in to the `catalog` user Linux account by running `su - catalog`.

  2. Access the `postgres` account by running `sudo -i -u postgres`.

  3. Create a database named `catalog` by running `createdb catalog`.

  4. Check to see is the `catalog` database was created by running `psql` and then `\l`.

  5. While in the `postgres` prompt, change the owner of the `catalog` database by running,
  `ALTER DATABASE catalog OWNER TO catalog`.

  6. Run `\l` to see if the `ALTER DATABASE` command was successful.

  7. Exit the `postgres` account and change back to the `ubuntu` user in Linux.


### Step 14: Install `Git` and Clone the Item Catalog Project

  1. Run `sudo apt-get install git` to install `git`.

  2. Chance the `sqlite` connection string in every Python file to:
  `engine = create_engine('postgresql://user:password@localhost/mydatabase')`

  3. Change the `app.run(host='localhost', port=8000)` the in `application.py` to `app.run()`.

  4. Rename the `project.py` file to `__init__.py` by running `mv project.py __init__.py`.

  5. Move to the `/var/www` directory by running `cd /var/www`.

  6. In the `/var/www` directory create a directory name `oracleBooksApp` by running `sudo mkdir oracleBooksApp`.

  7. Run `cd oracleBooksApp` to change into the `oracleBooksApp` directory.

  8. Clone the catalog project by running: `https://github.com/ENPlummer/Catalog-Linux-Project.git oracleBooksApp`.

    * The "oracleBooks" part at the of the URL changes the default file name from "Catalog-Linux-Project" to "oracleBooks", because hyphens tend to cause errors in the Apache server.


### Step 15: Set up a Virtual Environment and install Dependencies

  1. Run `sudo apt-get install python-pip` to install `pip`.

  2. Run `sudo pip install virtualenv` to install `virtualenv`.

  3. Create a virtual environment called `catalog` by running `sudo virtualenv catalog`.

  4. Run `source catalog/bin/activate` to activate the `catalog` virtual environment.

  5. Once in the virtual envionment install the following dependencies.

    1. `pip install flask`

    2. `pip install sqlalchemy`

    3. `pip install --upgrade oauth2client`

    4. `pip install httplib2`

    5. `pip install requests`

    6.  Outside of the virtual environment run `sudo apt-get install libpq-dev python-dev`

    7. `pip install psycopg2`

 6. Run `sudo python __init__.py` to test if the installation is successful and the app is running. It should disply "Running on http://localhost:5000/" or "Running on http://127.0.0.1:5000/". If you see either one of these messages then you have successfully configured your app.

 7. Deactivate the virtual environment by running `deactivate`.


### Step 16: Configure and Enable a New Virtual Host.

  1. Run `sudo nano /etc/apache2/sites-available/oracleBooksApp.conf`

  2. Add the following lines of code to the `oracleBooksApp.conf` file:

 ```
 <VirtualHost *:80>
    ServerName XX.XXX.XX.XXX
    ServerAdmin ebony.plummer@gmail.com
    WSGIScriptAlias / /var/www/oracleBooksApp/oraclebooksapp.wsgi
    <Directory /var/www/oracleBooksApp/oracleBooksApp/>
      Order allow,deny
      Allow from all
    </Directory>
    Alias /static /var/www/oracleBooksApp/oracleBooksApp/static
    <Directory /var/www/oracleBooksApp/oracleBooksApp/static/>
      Order allow,deny
      Allow from all
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
 </VirtualHost>
 ```

  3. Save and close the file.

  4. Run `sudo a2ensite oracleBooksApp` to enable the virtual host.


### Step 17: Create the `.wsgi` File

  1. Apache user the `.wsgi` file to serve the Flask app. Change to the `var/www/oracleBooksApp` by running
   `cd /var/www/oracleBooksApp`.

  2. Create a file named `oraclebooksapp.wsgi` by running `sudo nano oraclebooksapp.wsgi`.

  3. Add the following lines of code to the `oracleBooksApp.wsgi` file:

  ```
  activate_this = '/var/www/oracleBooksApp/oracleBooksApp/catalogvenv/bin/activate_this.py'
  with open(activate_this) as file_:
      exec(file_.read(), dict(__file__=activate_this))

  #!/usr/bin/python
  import sys
  import logging
  logging.basicConfig(stream=sys.stderr)
  sys.path.insert(0,"/var/www/oracleBooksApp/")

  from FlaskApp import app as application
  application.secret_key = 'Add your secret key'
  ```

### Step 18: Disable the Default Apache Site

  1.  Disable the default Apache site by running `sudo a2dissite 000-default.conf`. Once this command is ran the following prompt will be returned:

   ```
  Site 000-default disabled.
  To activate the new configuration, you need to run:
    service apache2 reload
  ```

  2. Run `sudo service apache2 reload`.


### Step 19: Change the Ownership of the Project Directories

* Since Apache runs as the `www-data` user the ownership of the project directories need to be changed to `www-data`.

* While in the `var/www` directory run
`sudo chown -R www-data:www-data oracleBooksApp/`


### Step 20: Set up the Database Schema and Populate the Database

  1. While in the `/var/www/oracleBooksApp/oracleBooksApp` directory, activate the virtual environment by running
  `. catalogvenv/bin/activate`.

  2. Run `python database_setup.py`.

  3. Run `python store_items.py`.

  4. Run `deactivate` to deactivate the virtual environment.

  5. Run `sudo service apache2 restart`.

  6. Open a browser window and check if the website is working by going to: http://18.208.212.235.


## Useful Commands

1. `lsb_release -a` Returns the version of Ubuntu that is running.

2. `whoami` Returns which user that you are logged as.

3. `sudo service apache2 restart`

4. `virtualenv --version`

5. `python --version`

6. `tail -f /var/log/apache2/error.log` Allows you to see the Apache error logs for debugging your application.

7. `sudo rm -rf NAME_OF_VIRTUALENV` Delete a virtual environment.

8. `dropdb NAME_OF_DATABASE` Drop a database if there have been changes to the database_setup.py file. This command should be ran as the `catalog` user.

9. `sudo apachectl stop` Apache needs to be stop in order to drop the database.

10. `sudo apachectl start`


## Works Cited

### Github Repositories

1. [bencam/linux-server-configuration](https://github.com/bencam/linux-server-configuration)

2. [iliketomatoes/linux_server_configuration](https://github.com/iliketomatoes/linux_server_configuration)

3. [kamalneel178/Linux_Server_Configuration](https://github.com/kamalneel178/Linux_Server_Configuration)

4. [adam-p/markdown-here](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet "Markdown Cheatsheet")


### Udacity

1. Udacity "Full Stack Developer Nanodegree", "Deploying to Linux Servers" Course Material.

2. [Internal Server Error due to No such file or directory: 'client_secrets.json'](https://knowledge.udacity.com/questions/54768)

3. [Internal Server Error](https://knowledge.udacity.com/questions/53916)

4. [ModuleNotFoundError: No module named 'database_setup'".](https://knowledge.udacity.com/questions/34674)

5. [ModuleNotFoundError: No module named 'item_catalog'](https://knowledge.udacity.com/questions/31871)

6. [Apache Import Error database_setup](https://knowledge.udacity.com/questions/18484)

7. [My apache2 Website Not Loading](https://knowledge.udacity.com/questions/49349)

8. [Psycopg2 import error](https://knowledge.udacity.com/questions/15878)

9. [No module named 'sqlalchemy'](https://knowledge.udacity.com/questions/44960)

10. [ImportError: No module named flask](https://knowledge.udacity.com/questions/9859)

11. [Trouble launching application from Apache: ModuleNotFoundError: No module named 'flask'](https://knowledge.udacity.com/questions/26923)

12. [Error : (InvalidClientSecretsError: ('Error opening file', 'client_secrets.json', 'No such file or directory', 2)](https://knowledge.udacity.com/questions/22774)


### SSH Connection

1. [ssh-keygen - Generate a New SSH Key](https://www.ssh.com/ssh/keygen/)

2. [SSH Config File](https://www.ssh.com/ssh/config/)

3. [How do I connect to a new Amazon Lightsail instance from my Mac?](https://stackoverflow.com/questions/46028907/how-do-i-connect-to-a-new-amazon-lightsail-instance-from-my-mac)

4. [How to SSH on a port other than 22](https://askubuntu.com/questions/264046/how-to-ssh-on-a-port-other-than-22)

5. [How do I restart the SSH service?](https://askubuntu.com/questions/103889/how-do-i-restart-the-ssh-service)

6. [How do you create an ssh key for another user?](https://serverfault.com/questions/323958/how-do-you-create-an-ssh-key-for-another-user)


### Linux Server Configuration

1. [How To Set Up a Firewall with UFW on Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-14-04)

2. [UFW Essentials: Common Firewall Rules and Commands](https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands)

3. [How To Deploy a Flask Application on an Ubuntu VPS](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)

4. [Initial Server Setup with Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04)

5. [How to Shutdown or Reboot Linux using Command Line](https://tecadmin.net/restart-linux-server-using-command-prompt/)

6. [How to quickly give users sudo privileges in Linux](https://www.techrepublic.com/article/how-to-quickly-give-users-sudo-privileges-in-linux/)

7. [How do I change my timezone to UTC/GMT?](https://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt)

8. [Reboot Linux System Command](https://www.cyberciti.biz/faq/howto-reboot-linux/)

9. [Deploy Flask & Python3 on Apache2 & Ubuntu](http://terokarvinen.com/2016/deploy-flask-python3-on-apache2-ubuntu)

10. [The Flask Mega-Tutorial Part XVII: Deployment on Linux](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-xvii-deployment-on-linux)

11. [Flask by Example – Setting up Postgres, SQLAlchemy, and Alembic](https://realpython.com/flask-by-example-part-2-postgres-sqlalchemy-and-alembic/)

12. [How do you view file permissions?](https://askubuntu.com/questions/528411/how-do-you-view-file-permissions)

13. [Target WSGI script not found or unable to stat [closed]](https://serverfault.com/questions/563459/target-wsgi-script-not-found-or-unable-to-stat)

14. [Database Configuration](https://flask-sqlalchemy.palletsprojects.com/en/2.x/config/)

15. [Updating Python on Ubuntu system](https://superuser.com/questions/241865/updating-python-on-ubuntu-system)


### PostgreSQL

1. [PostgreSQL 9.5.19 Documentation III. Server Administration](https://www.postgresql.org/docs/9.5/admin.html)

2. [SET ROLE](https://www.postgresql.org/docs/8.2/sql-set-role.html)

3. [CREATE ROLE](https://www.postgresql.org/docs/8.2/sql-createrole.html)

4. [ALTER ROLE](https://www.postgresql.org/docs/9.0/sql-alterrole.html)

5. [PostgreSQL ALTER DATABASE](http://www.postgresqltutorial.com/postgresql-alter-database/)

6. [How To Install and Use PostgreSQL on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04)

7. [How To Use Roles and Manage Grant Permissions in PostgreSQL on a VPS](https://www.digitalocean.com/community/tutorials/how-to-use-roles-and-manage-grant-permissions-in-postgresql-on-a-vps--2)

8. [How to change PostgreSQL user password?](https://stackoverflow.com/questions/12720967/how-to-change-postgresql-user-password)

9. [You need to install postgresql-server-dev-X.Y for building a server-side extension or libpq-dev for building a client-side application](https://stackoverflow.com/questions/28253681/you-need-to-install-postgresql-server-dev-x-y-for-building-a-server-side-extensi)

10. [Postgres is failing with 'could not open relation mapping file “global/pg_filenode.map” '](https://stackoverflow.com/questions/7445935/postgres-is-failing-with-could-not-open-relation-mapping-file-global-pg-fileno)

11. [Trouble installing psycopg2 on a virtualenv using python3](https://stackoverflow.com/questions/18316576/trouble-installing-psycopg2-on-a-virtualenv-using-python3)

12. [How to remove Postgres from my installation?](https://askubuntu.com/questions/32730/how-to-remove-postgres-from-my-installation)

13. [Could not open relation mapping file "global/pg_filenode.map" #96](https://github.com/PostgresApp/PostgresApp/issues/96)

14. [PostgreSQL Tutorial](http://www.tutorialspoint.com/postgresql/)

15. [Psycopg 2.8.4.dev0 Documentation: Installation](http://initd.org/psycopg/docs/install.html)

16. [How do I correct this value too long for type character varying(5)?](https://dba.stackexchange.com/questions/159380/how-do-i-correct-this-value-too-long-for-type-character-varying5)


### Miscellaneous

1. [Command Line Cheat Sheet](https://www.git-tower.com/blog/command-line-cheat-sheet/)

2. [How do I rename a directory via the command line?](https://askubuntu.com/questions/56326/how-do-i-rename-a-directory-via-the-command-line)

3. [Pipenv & Virtual Environments](https://docs.python-guide.org/dev/virtualenvs/)

4. [Alternative to execfile in Python 3? [duplicate]](https://stackoverflow.com/questions/6357361/alternative-to-execfile-in-python-3)


* Orignal project URL: 18.208.212.235












