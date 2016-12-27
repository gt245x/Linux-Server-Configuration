#Linux Server Configuration

**Linux Server Configuration** is a project for Udacity Fullstack program. This project deploys the Catlog project developed earlier in the course onto a virtual server using Amazon's Elastic Compute Cloud.

#Procedure
The procedure will show step-by-step the process that was taken in order to configure the virtual server.
+ public URL : http://ec2-35-166-142-174.us-west-2.compute.amazonaws.com/
+ IP Address : 35.166.142.174

##1 Create Development Environment
1. Create a new development environment
2. Download private key
3. Move the private key file into the folder ~/.ssh

>       mv ~/Downloads/udacity_key.rsa ~/.ssh/    

4. Open the terminal and chmod to 600 for the downloaded key

>       chmod 600 ~/.ssh/udacity_key.rsa

5. In the terminal, ssh to ther server by:

>       ssh -i ~/.ssh/udacity_key.rsa root@35.166.142.174


##2. Create new user with permission to sudo
References for the procedure/steps for creating new user with permission to sudo can be found [here](https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-ubuntu-quickstart)

1. Once ssh to the server, adduser called grader

>       adduser grader 

2. Set and confirm the new user's password at the prompt. A strong password is highly recommended

>       Set password prompts:
        Enter new UNIX password:
        Retype new UNIX password:
        passwd: password updated successfully

3. Move the authorization key from the root user to the new user grader .ssh folder and change owner of the file from root to grader  
4. Use the usermod command to add the user (grader) to the sudo group

>       usermod -aG sudo grader

5. Use the su command to switch to the grader account

>       su grader

6. Verify that the grader has sudo account by prepending 'sudo' to the command that you want to run with superuser privileges like listing contents of the /root directory

>       sudo ls -la /root

7. The first time you use sudo in a session, you will be prompted for the password of the user account. Enter the password to proceed.


##3. Update all installed packages
1. To update installed packages, first get information on the newest versions of packages and their dependencies

>       sudo apt-get update

2. Next, update the softwares

>       sudo apt-get upgrade

3. Remove any packages no longer required that we can automatially remove

>        sudo apt-get autoremove

4. Install software finger

>       sudo apt-get install finger

5. Automatic updates can be installed as shown by the [documentation](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)

>       sudo apt install unattended-upgrades


##4 Change the SSH port from 22 to 2200 and disable root login
Procedure is shown [here](https://www.godaddy.com/help/changing-the-ssh-port-for-your-linux-server-7306)

1. SSH to the server using the graders login info
2. Run the following command 

>       sudo nano /etc/ssh/sshd_config

3. Locate the following line:

>       # Port 22

4. Remove # and change 22 to 200
5. Locate the following line:

>       PermitRootLogin yes

6. Change the value 'yes' to 'no'
7. Restart the sshd service by running the following command:

>       service sshd restart


##5 Configure UFW
1. Verify that firewall is inactive by:

>       sudo ufw status

2. Deny all incoming request

>       sudo ufw default deny incoming

3. Allow all outgoing request

>       sudo ufw default allow outgoing

4. Verify that the firewall is still inactive

>       sudo ufw status

5. Allow SSH at port 2200

>       sudo ufw allow 2200

6. Allow HTTP on port 80

>       sudo ufw allow 80/tcp

7. Allow NTP on port 123

>       sudo ufw allow 123


##6. Configure the local timezone to UTC
Source is [here](https://help.ubuntu.com/community/UbuntuTime#Using_the_Command_Line_.28terminal.29)

1. Type the following on the command line

>       sudo dpkg-reconfigure tzdata

2. Follow the directions to select UTC timezone


##7. Install and configure Apache to serve a Python mod_wsgi application
Procedure is shown [here](http://blog.udacity.com/2015/03/step-by-step-guide-install-lamp-linux-apache-mysql-python-ubuntu.html)

1. To install apache, type the following command:

>       sudo apt-get install apache2

2. After installation, check the server address and it should show the ubuntu web page saying that it's working.

3. Install mod-wsgi tool and python-setuptools for serving python applications

>       sudo apt-get install python-setuptools libapache2-mod-wsgi

4. Restart apache server after installation

>       sudo service apache2 restart


##8. Install and configure Git
The steps for installing and configuring git are shown [here](https://www.digitalocean.com/community/tutorials/how-to-install-git-on-ubuntu-14-04#how-to-set-up-git)

1. Install git by this command

>       sudo apt-get install git

2. Configure the user name by this command

>       git config --global user.name "Your Name"

3. Configure the user e-mail by this command

>       git config --global user.email "youremail@domain.com"

4. We can see all of the configuration items that have been set by typing:

>       git config --list

5. The git info is stored in the configuration file, which can be optionally edited by:

>       nano ~/.gitconfig


##9. How to Deploy a Flask Application on an Ubuntu VPS
The steps for deploying a flask application are shown [here](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps) and [here](https://www.youtube.com/watch?v=bMEAtCuFoiw). The deployment will serve to ensure that the App is correctly deployed before the catalog peoject is deployed.

###Step One - Install and Enable mod-wsgi

Open the terminal and type the following command to install mod_wsgi:
>       sudo apt-get install libapache2-mod-wsgi python-dev

To enable mod_wsgi, run the following command:
>       sudo a2enmod wsgi 

###Step Two - Creating a Catalog App
In this step, we will create a catalog app. We will place our app in the /var/www directory.

Use the following command to move to the /var/www directory:

>       cd /var/www 

Create an application directory called CatalogApp

>       sudo mkdir CatalogApp

Move inside this directory using the following command:

>       cd CatalogApp

Create another directory CatalogApp by giving following command:

>       sudo mkdir CatalogApp

Then, move inside this directory and create two subdirectories named static and templates using the following commands

        cd CatalogApp
        sudo mkdir static templates

Your directory structure should now look like this:

        |----CatalogApp
        |---------CatalogApp
        |--------------static
        |--------------templates

Now, create the __init__.py file that will contain the catalog application logic.

        sudo nano __init__.py 

Add following logic to the file:

        from flask import Flask
        app = Flask(__name__)
        @app.route("/")
        def hello():
        return "Hello, I love Linux Server Configuration!"
        if __name__ == "__main__":
        app.run()

Save and close the file.

###Step Three - Install Flask

Setting up a virtual environment will keep the application and its dependencies isolated from the main system. Changes to it will not affect the cloud server's system configurations.

In this step, we will create a virtual environment for our flask application.

We will use pip to install virtualenv and Flask. If pip is not installed, install it on Ubuntu through apt-get.

        sudo apt-get install python-pip 

If virtualenv is not installed, use pip to install it using following command:

        sudo pip install virtualenv 

Give the following command (where venv is the name you would like to give your temporary environment):

        sudo virtualenv venv

Now, install Flask in that environment by activating the virtual environment with the following command:

        source venv/bin/activate 

Give this command to install Flask inside:

        sudo pip install Flask 

Next, run the following command to test if the installation is successful and the app is running:

        sudo python __init__.py 

It should display “Running on http://localhost:5000/” or "Running on http://127.0.0.1:5000/". If you see this message, you have successfully configured the app.

To deactivate the environment, give the following command:

        deactivate


###Step Four - Configure and Enable a New Virtual Host

Issue the following command in your terminal for newer versions of Ubuntu: 

        sudo nano /etc/apache2/sites-available/CatalogApp.conf

Add the following lines of code to the file to configure the virtual host. Be sure to change the ServerName to your domain or cloud server's IP address:

        <VirtualHost *:80>
                    ServerName IP Address
                    ServerAdmin admin@mywebsite.com
                    WSGIScriptAlias / /var/www/CatalogApp/catalogapp.wsgi
                    <Directory /var/www/CatalogApp/CatalogApp/>
                        Order allow,deny
                        Allow from all
                    </Directory>
                    Alias /static /var/www/CatalogApp/CatalogApp/static
                    <Directory /var/www/CatalogApp/CatalogApp/static/>
                        Order allow,deny
                        Allow from all
                    </Directory>
                    ErrorLog ${APACHE_LOG_DIR}/error.log
                    LogLevel warn
                    CustomLog ${APACHE_LOG_DIR}/access.log combined
        </VirtualHost>

Save and close the file.

Enable the virtual host with the following command:

        sudo a2ensite CatalogApp


###Step Five - Create the .wsgi File

Apache uses the .wsgi file to serve the Catalog app. Move to the /var/www/CatalogApp directory and create a file named catalogapp.wsgi with following commands:

        cd /var/www/CatalogApp
        sudo nano catalogapp.wsgi

Add the following lines of code to the catalogapp.wsgi file:

        #!/usr/bin/python
        import sys
        import logging
        logging.basicConfig(stream=sys.stderr)
        sys.path.insert(0,"/var/www/CatalogApp/")

        from CatalogApp import app as application
        application.secret_key = 'Add your secret key'

Now your directory structure should look like this:

        |--------CatalogApp
        |----------------CatalogApp
        |-----------------------static
        |-----------------------templates
        |-----------------------venv
        |-----------------------__init__.py
        |----------------catalogapp.wsgi


###Step Six - Restart Apache

Restart Apache with the following command to apply the changes:

        sudo service apache2 restart 

Then open up the ip address and it should show that the CatalogApp was successfully deployed


##10. Deploy Catalog Project
Follow the steps on Procedure 9.

Use the following command to move to the /var/www directory:

>       cd /var/www  

Create an application directory called catalog

>       sudo mkdir catalog

Move inside this directory using the following command:

>       cd catalog

Create another directory catalog by giving following command:

>       sudo mkdir catalog

Next, clone catalog project repository from Github

>        https://github.com/gt245x/Catalog.git

Move the contents of catalog project to the catalog directory and delete the empty directory 
Disable .git directory by using .htaccess

cd to the root of the catlog app

>       cd /var/www/catalog

create a .htaccess file 

>       sudo nano .htaccess

Paste the following into the .htaccess file and save. It works for all .git directories on the server.

>       Redirect 404 /\.git


###Install packages
Follow procedure in section 9. Activate the virtual environment to download the packages.
- sudo virtualenv venv
- source venv/bin/activate
- sudo pip install sqlalchemy
- sudo pip install requests
- pip2 install httplib2 --upgrade
- sudo apt-get install libpq-dev python-psycopg2
- deactivate


##11. Install and configure PostgreSQL
The procesdures for installing postgreSQL can be find [here](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps) and [here](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-14-04)

1. Install PostgreSQL by using the following command line:

>       sudo apt-get install postgresql postgresql-contrib

2. Do not allow Remote Connections. Simplest way to avoid potential attack vector is to not allow remote connections to the database which is the current default when installing PostgreSQL.

3. Double check that no remote connections are allowed by looking in the host based authentication file:

>       sudo nano /etc/postgresql/9.3/main/pg_hba.conf

4. Create a new user name catalog that has limited permission to database

>       sudo adduser catalog

5. postgres user was created during install. Switch to postgres user:

>       sudo su postgres

6. You can get a Postgres prompt immediately by typing:

>       psql

7. Create database user with password "db-password"

>       CREATE USER catalog WITH PASSWORD 'db-password';

8. You can alter the permissions of any role by typing:

>       ALTER ROLE role_name WITH optional_permissions;

9. We can now create a database 'catalog' owned by "catalog":

>       CREATE DATABASE catalog WITH OWNER catalog;

10. Connect to the catalog database

>       \c catalog

11. List the current roles and their attributes by typing:

>       \du

12. We can now connect to the database and lock down the permissions to only let catalog create tables:

>       REVOKE ALL ON DATABASE catalog FROM PUBLIC;
>       REVOKE ALL ON SCHEMA public FROM PUBLIC;

13. Grant permission to owner catalog

>       GRANT CONNECT ON DATABASE catalog to catalog;
>       GRANT ALL ON SCHEMA public to catalog;

14. Access privileges can be viewed by:

>       \l

15. Exit out of the PostgreSQL prompt by typing:

>       \q

More tutorial on postgres installation and setup can be found [here](https://www.youtube.com/watch?v=67XGzdzv9k0)

##12. Update catalog Project files

1. cd to /var/www/catalog/catalog
2. change the project.py name to __init__.py

>       mv project.py __init__.py

3. Open the __init__.py file and change the engine = create engine... line. Change from sqlite to postgressql:

>      engine = create_engine('postgresql://catalog:db-password@localhost/catalog')

4. As discussed on the [forum](https://discussions.udacity.com/t/client-secret-json-not-found-error/34070). Update the absolute path for client_secrets.json

>       CLIENT_ID = json.loads(
    open(r'/var/www/catalog/catalog/client_secrets.json', 'r').read())['web']['client_id']

    oauth_flow = flow_from_clientsecrets(r'/var/www/catalog/catalog/client_secrets.json', scope='')
>       

5. Update the absolute path for fb_client_secrets.json

>       app_id = json.loads(open(r'/var/www/catalog/catalog/fb_client_secrets.json', 'r').read())[
        'web']['app_id']


5. open the catalog_database.py file and change the engine = create engine... line

>       >      engine = create_engine('postgresql://catalog:db-password@localhost/catalog')

6. Create the postgresql database

>       python catalog_database.py

7. Populate the postgresql database

>       python lotsofitems.py


##13 Run catalog application
1. Update catalog.wsgi with the applicaton secret key.
2. open the configuration file for the catalog. 

>       sudo nano /etc/apache2/sites-available/catalog.conf     

3. Update the serverName with the ip address.
4. Add the ServerAlias name below the ServerName

>       ServerAlias ec2-35-166-142-174.us-west-2.compute.amazonaws.com

5. Restart Apache

>       sudo service apache2 restart

6. If everything works fine, the catalog should show up on the webserver.
7. Install oauth2client 

>       sudo pip install --upgrade oauth2client

8. Update google login authorization:
- On google APU developer console, navigate to the category app under Credentials and Edit settings
- Add http://ec2-35-166-142-174.us-west-2.compute.amazonaws.com to Authorized Javascript origins
- Add http://ec2-35-166-142-174.us-west-2.compute.amazonaws.com/oauth2callback to Authorized redirect URIs
- save and close

9. Update facebook login authorization:
- On facebook developer console, navigate to the app under settings
- Update the valid oAuth redirect URIs to include http://ec2-35-166-142-174.us-west-2.compute.amazonaws.com
- save and close

##14. Miscellanous
Various additional functionality are needed for the proper function of the server. Some of the functions include:

###Error logs
[source](https://www.a2hosting.com/kb/developer-corner/apache-web-server/viewing-apache-log-files)
The error logs is where Apache records information about any errors or anomalies it encounters. To view the last 100 lines in the error log:

>       sudo tail -100 /etc/httpd/logs/error_log

To search for a particular item (ex. invalid) in the error log, use the grep command

>       sudo grep -i invalid /etc/httpd/logs/error_log

###Access logs
Apache uses the access log files to record information about every visitor to the site. To view the last 100 lines in the access log:

>       sudo tail -100 /etc/httpd/logs/access_log

### How to fix Apache - Could not reliably determine the server's fully qualified domain, using 127.0.1.1 for ServerName

To fix this issue:
>       sudo nano /etc/apache2/apache2.conf

Add ServerName localhost in the very end of the file.

### How to Protect SSH with Fail2Ban
A service called fail2ban can mitigate the problem that comes with inherent risks and attacks on the server. The steps in installing and configuring fail2Ban are shown [here](https://www.digitalocean.com/community/tutorials/how-to-protect-ssh-with-fail2ban-on-ubuntu-14-04)

1. Download and install fail2ban package

>       sudo apt-get install fail2ban

2. Copy the existing jail.conf file to jail.local

>       sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local

3. Open the jail.local file

>       sudo nano /etc/fail2ban/jail.local

4. Adjust bantime parameter to 180 secs or 30 mins

>       bantime = 1800

5. findtime and maxrentry establish conditions for an illegitimate user to be banned.

>       findtime = 600
        maxretry = 3       

6. Update destemail to get ban reports

>       destemail = root@localhost


### Command to list, add, delete and modify users
[Source](http://askubuntu.com/questions/410244/a-command-to-list-all-users-and-how-to-add-delete-modify-users/414561)

To list all users in the terminal, use the command below:

>       cut -d: -f1 /etc/passwd

To remove/delete a user, first:

>       sudo userdel username

Then delete the home directory for the deleted user account:

>       sudo rm -r /home/username

To modify the username of a user:

>       usermod -l new_username old_username

To change the password for a user:

>       sudo passwd username

To change the shell for a user:

>       sudo chsh username

To change the details for a user (for example real name):

>       sudo chfn username


