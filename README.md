# Linux Server Configuration
This is the final project for Udacity "Full Stack Web Developer Nanodegree".

Below is the set of instructions on how to set up a Ubuntu Linux server to host a Movie Catalog app built with Flask.

Visit the link "http://www.35.200.146.141.xip.io/" to check out the hosted application.

# Software to install during the configuration
- Apache2
- mod_wsgi
- PostgreSQL
- git
- virtualenv
- httplib2
- oauth2client
- SQLAlchemy
- Flask

# Instructions to setup
- Create an instance with Google Cloud Platform
- Sign in to Cloud using a Google account
- Choose the VM instances and create instance.
- In Boot disk choose 'Ubuntu 16.04 LTS' option.
- Give the instance a unique name and click allow http and https traffic and Create.
- Click SSH connect and update installed packages
    - sudo apt-get update
    - sudo apt-get upgrade
- Since according to requirements the SSH port 22 should be disabled in terminal open 
    -sudo vi /etc/ssh/sshd_config"  
- Change port form 22 to 2200. Run sudo service ssh restart
- Under Networking VPC network open Firewall rules. Change ssh port to tcp:2200.
<img src="pics/firewall.png" width="800">
- Now under SSH button choose open browser window on custom port. 
- The terminal should open on 2200 port now.
<img src="pics/port.png" width="800">

# Configure the ufw Firewall
- Check if the ufw is active by running sudo ufw status
- Run sudo ufw default deny incoming to set the ufw firewall to block everything coming in
- Run sudo ufw default allow outgoing to set the ufw firewall to allow everything outgoing
- Run sudo ufw allow ssh to set the ufw firewall to allow SSH
- Run sudo ufw allow 2200/tcp to allow all tcp connections for port 2200 so that SSH will work
- Run sudo ufw allow www to set the ufw firewall to allow a basic HTTP server
- Run sudo ufw allow 123/udp to set the ufw firewall to allow NTP
- Run sudo ufw deny 22 to deny port 22
- Run sudo ufw enable to enable the ufw firewall
- Run sudo ufw status to check which ports are open and to see if the ufw is active

# Create a new user by the name grader
- Run sudo adduser grader
- Fill out information for the new grader user
- To switch to the grader user, run su - grade
- To give grader sudo permission Run sudo visudo
- Add the following line below root ALL=(ALL:ALL) ALL 'grader ALL=(ALL:ALL) ALL'
- Save and close the visudo file
- Verify that grader has sudo permissions.

# To allow grader to log into VM
- Run ssh-keygen on the local machine
- Choose a key name eg: grader_key
- Log in to the virtual machine
- Switch to grader's home directory, and create a new directory called .ssh
- Run touch .ssh/authorized_keys
- Open the grader_key.pub and paste them into .ssh/authorized_keys file on VM
- Change permissions chmod 700 .ssh , chmod 644 .ssh/authorized_keys.
- To make sure key-based authentication is forced open /etc/ssh/sshd_config file '# Change to no to disable tunnelled clear text passwords'if the next line is 'PasswordAuthentication yes', change the 'yes' to 'no', save and exit the file, run sudo service ssh restart
- Log in as the grader ssh -i ~/.ssh/grader_key -p 2200 grader@XX.XX.XX.XX

# Configure the local timezone to UTC
Run sudo dpkg-reconfigure tzdata, select none of the above, then UTC.

# Install and configure Apache to serve a Python mod_wsgi application
- Install Apache web server:
    - $ sudo apt-get install apache2
- Open a browser and open your public ip address, e.g. http://52.25.0.41/ - Apache page should be displayed.
- Install mod_wsgi for serving Python apps from Apache and the helper package python-setuptools:
    - $ sudo apt-get install python-setuptools libapache2-mod-wsgi
- Restart the Apache server for mod_wsgi to load:
    - $ sudo service apache2 restart
- Create an empty Apache config file with the hostname:
    - $ echo "ServerName HOSTNAME" | sudo tee /etc/apache2/conf-available/fqdn.conf
- Enable the new config file:
    - $ sudo a2enconf fqdn

# Install git and clone the MovieCatalog project
- Run sudo apt-get install git
- Create a directory called 'catalog' in the /var/www/ directory
- Change to the 'catalog' directory, and clone the catalog project:
    - sudo git clone https://github.com/----yourgitproj------.git catalog
- Change the name of the application.py file to __init__.py by running mv application.py __init__.py
- In __init__.py replace app.run(host='0.0.0.0', port=8000) with app.run()
- Make the GitHub repository inaccessible:
    - Create and open .htaccess file:
    - $ cd /var/www/catalog/ and $ sudo vi .htaccess
    - Paste in the following: RedirectMatch 404 /\.git

# Switch the database in the application from SQLite to PostgreSQL
- replace engine with engine = create_engine('postgresql://catalog:INSERT_PASSWORD_FOR_DATABASE_HERE@localhost/catalog') 

# Setup for deploying a Flask Application on Ubuntu VPS
- Refer to the website and replace flaskapp with catalog.
- https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

# Install needed modules & packages
- Activate virtual environment:
    $ source venv/bin/activate
- Install httplib2 module in venv:
    $ pip install httplib2
- Install requests module in venv:
    $ pip install requests
- *Install flask.ext.seasurf (only seems to work when installed globally):
    $ *sudo pip install flask-seasurf
- Install oauth2client.client:
    $ sudo pip install --upgrade oauth2client
- Install SQLAlchemy:
    $ sudo pip install sqlalchemy
- Install the Python PostgreSQL adapter psycopg:
    $ sudo apt-get install python-psycopg2

# Install and configure PostgreSQL
- Refer to the Guide 
- https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps

# Run application
- Restart Apache: $ sudo service apache2 restart
- Open a browser and put in your public ip-address 35.200.146.141
- View the last 20 lines in the error log: $ sudo tail -20 /var/log/apache2/error.log

# To get the client_secrets.json files working
- Authenticate login through Google:
<img src="pics/auth.png" width="800">

- Google will provide a client ID and client secret for the project, download the JSON file, and copy and paste the contents into the client_secrets.json file
- Add the client ID to the templates/login.html file in the project directory
- Add the complete file path for the client_secrets.json file in the __init__.py file; change it from 'client_secrets.json' to '/var/www/catalog/catalog/client_secrets.json'

