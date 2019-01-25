# Udacity_LinuxServerConfig
This project is the last project of Udacity Full Stack Web Developer Nanodegree Program. It is to configure a bare bones linux server to secure and efficient web application host a web application.

## Server's IP address and SSH port
1. Server's IP: 34.194.172.98
2. SSH Port: 2200

## URL to the hosted web application
    http://34.197.172.98:2200/catalog

## Software installed
1. Apache2
2. Git
3. Python2.7
4. libapache2-mod-wsgi-py
5. postgresql

## Configuratin changes
1. Update all currently installed packages:
    * sudo apt-get update
    * sudo apt-get upgrade
2. Create a new user account, grader and give it the permission to sudo
    * sudo adduser grader
    * sudo cp /etc/sudoers.d/90-cloud-init-users /etc/sudoers.d/grader
    * update ubuntu to grader in /etc/sudoers.d/grader
    * sudo vi /etc/ssh/sshd_config to update the line "PasswordAuthentication no" to "PasswordAuthentication yes"
    * Generate key pairs: sudo ssh-keygen, save the key files to ~/.ssh/linuxCourse and linuxCourse.pub
    * open a command prompt window, ssh grader@34.194.172.98 -p 22
      in command prompt window, mkdir .ssh
    * create the key file in grader account: touch .ssh/authorized_keys
    * copy paste the public key from ubuntu account into the key files in grader account:
        I. sudo cp /root/.ssh/linuxCourse.pub /home/grader/.ssh/authorized_keys
        II. chown -R grader:grader .ssh
        III. chmod 700 .ssh
        IV. chmod 644 .ssh/authorized_keys
    * login to server using the private key and the passphrase:
        sudo ssh grader@34.194.172.98 -p 22 -i /root/.ssh/linuxCourse
3. Change the SSH port from 22 to 2200, before testing the access via 2200, don't delete port 22
    * sudo vi /etc/ssh/sshd_config
    * update the port number from 22 to 2200 in /etc/ssh/sshd_config.
    * restart ssh: sudo service ssh restart
    * use following command to login to the server:
        sudo ssh grader@34.194.172.98 -p 2200 -i /root/.ssh/linuxCourse
4. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200),        HTTP (port 80), and NTP (port 123).
    * Deny incoming traffic, open outgoing traffic<br/>
        sudo ufw default deny incoming<br/>
        sudo ufw default allow outgoing<br/>
    * Open incoming connections for ssh, http and ntp:
        sudo ufw allow 2200/tcp<br/>
        sudo ufw allow www<br/>
        sudo ufw allow ntp<br/>
    * Turn on the firewall:
        sudo ufw enable<br/>
    * Check firewall status and confirm the rules setup:
        sudo ufw status<br/>
5. Configure the local timezone to UTC
    * sudo dpkg-reconfigure tzdata
6. Remove remote access for root:
    * in /etc/ssh/sshd_config, make following changes:
        PermitRootLogin no<br/>
        AllowUsers grader<br/><br/>
6. Install following packages/libraries:
    * sudo apt-get install apache2
    * sudo apt install python2.7 python-pip
    * sudo apt-get install libapache2-mod-wsgi-py3
    * sudo apt-get install postgresql
    * sudo pip install SQLAlchemy
    * sudo pip install psycopg2
    * sudo pip install flask
    * sudo pip install flask_httpauth
    * sudo pip install oauth2client
    * sudo pip install requests
7. Git clone the catalog project to the server and move it to /var/www/html/catalog
8. Create database catalogMngr:
    * sudo -u postgres createuser -P catalog (password catalog)
    * create an empty database catalogMngr:
    * sudo -u postgres createdb -O catalog catalogMngr
    * connect to catalogMngr: sudo -u postgres psql catalogMngr
    * postgres=# grant all privileges on database catalogMngr to catalog;
9. Run db_setup.py and insert_data.py to create tables and populate data in the tables
10. Deploy catalogMng to apache:
    * Create app.wsgi:
        #!/usr/bin/python<br/>
        import sys<br/>
        import logging<br/>
        logging.basicConfig(stream=sys.stderr)<br/>
        sys.path.insert(0, '/var/www/html/catalog')<br/><br/>
        from application import app as application<br/>
        application.secret_key = 'super_secret_key'<br/><br/>
    * Configure Apache for the application:
        in /etc/apache2/sites-enabled/000-default.conf, adding following code right before :<br/>
        <VirtualHost>
            WSGIDaemonProcess catalog user=grader group=grader threads=5<br/>
            WSGIScriptAlias /catalog /var/www/html/catalog/app.wsgi<br/>
            WSGIProcessGroup catalog<br/><br/>
            <Directory /var/www/html/catalog> <br/>
                WSGIApplicationGroup %{GLOBAL} <br/>
                Order deny,allow <br/>
                Allow from all <br/>
            </Directory><br/>
        </VirtualHost>
    * Update and put full path of client_secrets.json in the main program file, and update app.run(0.0.0.0, port=8000) to app.run()
11. restart Apache:
    * sudo service apache2 restart

## Acknowledgements & Reference
1. https://github.com/SteveWooding/fullstack-nanodegree-linux-server-config
2. https://github.com/bad2thuhbone/Linux-Server-Project-6
3. https://help.ubuntu.com/community/PostgreSQL
4. https://code.google.com/archive/p/modwsgi/wikis/ConfigurationDirectives.wiki



    
            




