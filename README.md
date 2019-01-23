# Udacity_LinuxServerConfig
This project is the last project of Udacity Full Stack Web Developer Nanodegree Program. It is to configure a bare bones linux server to secure and efficient web application host a web application.

# Server's IP address and SSH port
1. Server's IP: 54.158.50.36
2. SSH Port: 2200

# URL to the hosted web application

# Software installed
1. Apache2
2. Git
3. Python3
4. libapache2-mod-wsgi-py3
5. postgresql

# Configuratin changes
1. Update all currently installed packages:
    sudo apt-get update
    sudo apt-get upgrade
2. Create a new user account, grader and give it the permission to sudo
    sudo adduser grader
    sudo cp /etc/sudoers.d/90-cloud-init-users /etc/sudoers.d/grader
    update ubuntu to grader in /etc/sudoers.d/grader
    sudo vi /etc/ssh/sshd_config to update the line "PasswordAuthentication no" to "PasswordAuthentication yes"
    Generate key pairs: sudo ssh-keygen, save the key files to /root/.ssh/linuxCourse and linuxCourse.pub
    reboot the server in Lightsail console
    open a command prompt window, ssh grader@54.158.50.36 -p 22
    in command prompt window, mkdir .ssh
    create the key file in grader account: touch .ssh/authorized_keys
    copy paste the keys from ubuntu account into the key files in grader account:
        sudo cp /root/.ssh/linuxCourse.pub /home/grader/.ssh/authorized_keys
        chmod 700 .ssh
        chmod 644 .ssh/authorized_keys
    login to server using the key pair and the passphrase:
        sudo ssh grader@54.158.50.36 -p 22 -i /root/.ssh/linuxCourse
3. Change the SSH port from 22 to 2200:
    sudo vi /etc/ssh/sshd_config
    update the port number from 22 to 2200 in /etc/ssh/sshd_config.
4. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200),        HTTP (port 80), and NTP (port 123).
    Deny incoming traffic, open outgoing traffic
        sudo ufw default deny incoming
        sudo ufw default allow outgoing
    Open incoming connections for ssh, http and ntp:
        sudo ufw allow 2200/tcp
        sudo ufw allow www
        sudo ufw allow ntp
    Turn on the firewall:
        sudo ufw enable
    Check firewall status and confirm the rules setup:
        sudo ufw status
5. Configure the local timezone to UTC
    sudo dpkg-reconfigure tzdata
6. Install following packages/libraries:
    sudo apt-get install apache2
    sudo apt install python3 python-pip
    sudo apt-get install libapache2-mod-wsgi-py3
    sudo apt-get install postgresql
    
    
            




