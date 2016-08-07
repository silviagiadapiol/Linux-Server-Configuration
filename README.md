Dear reviewer,


My VM is running on 

http://ec2-52-26-95-12.us-west-2.compute.amazonaws.com/

and its IP address is 52.26.95.12.
(I used http://ping.eu/rev-lookup/ to find out the fully qualified domain name of my VM.)

Please log in as user grader at SSH port 2200:
`ssh -i ~/.ssh/udacity_key.rsa -p2200 root@52.26.95.12`
`su grader`
As required, sudo commands prompt for user password (reported in the "note for reviewer" section).

I updated all currently installed packages to most recent version available

As you can see in the file: /etc/ssh/sshd_config
SSH port has been changed to 2200
`Port 2200`
Root login with SSH is disabled
`PermitRootLogin no`
Password login is disabled for all users
`PasswordAuthentication no`.

I set up the Uncomplicated Firewall (UFW) according to specification 
(only ports 80, 123 and 2200 have been allowed), to verify that please call

$ sudo ufw status verbose


I set up the system's timezone to UTC with the command

$ sudo dpkg-reconfigure tzdata

as I found at
https://help.ubuntu.com/community/UbuntuTime#Using_the_Command_Line_.28terminal.29


I installed apache2 and mod_wsgi with all dependencies with
`sudo apt-get install apache2`
`sudo apt-get install libapache2-mod-wsgi`
I created a sample file with a 'Hello World' application to test it
`/var/www/html/myapp.wsgi`
configurating the alias at
`/etc/apache2/sites-enabled/000-default.conf`

To reach this result I read the documentation at:
https://code.google.com/p/modwsgi/wiki/QuickConfigurationGuide
http://modwsgi.readthedocs.io/en/develop/configuration-directives/WSGIScriptAlias.html


I installed Postgresql with all dependencies and I configured it following the documentation at https://help.ubuntu.com/community/PostgreSQL
I found that the basic installation of postgres on Ubuntu does not allow remote connections by default, to verify this I checked the file:

/etc/postgresql/9.3/main/postgresql.conf

In addition, the port posgreslq is running on 5432, (see /etc/postgresql/9.3/main/postgresql.conf) that has not been opened in ufw.

I then created the database user 'catalog' and the postgres database 'babystuff'. I granted this user all privileges on the database 'babystuff'.
(I did not create a system user 'catalog', the owner of all application is the user 'grader').

I adjusted /etc/postgresql/9.3/main/pg_hba.conf, to connect to the database with a password. To connect to the babystuff database as the user catalog use
`sudo -u postgres psql -U catalog babystuff`
(with the password given in the "note for reviewer" section)


I created the following files and directories for my application:

/var/www/catalog/Catalog/application.wsgi -- WSGI wrapper file
/var/www/catalog/Catalog -- where I cloned the git checkout of the application (from my github repository at https://github.com/silviagiadapiol/Catalog)

I have changed the DATABASE_URL in the application code (db_setup.py) to point to the local postgres database: I substitute 
`engine = create_engine('sqlite:///babystuff.db')`
with
`engine = create_engine('postgresql://catalog:annabella@localhost:5432/babystuff')`
in the db_setup.py, db_populator.py and application.py files

I installed the python modules for flask and sqlalchemy as they where necessary for the application to work.

The current working directory of a python app running through WSGI is not necessarily the one where the python files are stored, so relative paths are no longer working. I therefore had to change the paths of the json files to be able to read settings files from the file system.


To create the WSGI wrapper file. I followed

http://flask.pocoo.org/docs/0.11/deploying/mod_wsgi/


The apache/mod_wsgi configuration can be found in

/etc/apache2/sites-enabled/000-default.conf

I used

http://flask.pocoo.org/docs/0.10/deploying/mod_wsgi/ and
https://code.google.com/p/modwsgi/wiki/ConfigurationDirectives
for the apache/mod_wsgi configuration.

I registered the new URL of my application with the OAuth providers I'm using:

Google+: https://console.developers.google.com/
Facebook: https://developers.facebook.com/

To complete this project I used the additional resources mentioned on the guide
(Markedly underwhelming and potentially wrong resource list for P5, Project 5 Resources, P5 How I got through it) and rely on advices present in the https://github.com/skh/fsnd-linux-server-config/blob/master/README.md posted in the forum


