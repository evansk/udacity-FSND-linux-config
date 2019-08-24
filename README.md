# Linux Server Configuration
Udacity Full Stack Nano Degree Project 5

## Server Details

- I.P. Address: 3.226.253.225
- Port: 2200
- URL: http://3.226.253.225/

## Configuration

### Amazon LightSail
1. Go to [Amazon LightSail](https://signin.aws.amazon.com) and sign in with your AWS account or create an account.
2. Click the button to create an instance
3. Choose a Linux/Unix Platform and under the OS Only tab choose an Ubuntu package
4. Name the instance and choose a payment plan. The cheapest plan is usually free for the first month.
5. Click the button to create the instance and wait for it to start running.
6. Access the instance using ssh and the provided IP Address

### Update Software
Update the server's software using `sudo apt-get update` and then `sudo apt-get upgrade`

### SSH Port Configuration

1. Under the Networking tab add a custom rule to the Firewall
    - Port `2200` Protocol `TCP` for SSH
2. Edit the file `/etc/ssh/sshd_config`
    - You will see the line `#Port 22`
    - Change this to `Port 2200`
3. Restart SSH using `sudo service ssh restart`
4. End your current SSH session then SSH into the server again using port 2200
    - If you cannot access the server you have done something wrong

Resource: [Changing the SSH Port for Your Linux Server](https://www.godaddy.com/help/changing-the-ssh-port-for-your-linux-server-7306)

### Firewall Configuration
#### Amazon LightSail
1. Navigate to the Manage tab of you Amazon LightSail instance
2. Under the Networking tab add a custom rule to the Firewall
    - Port `123`  Protocol `UDP` for NTP
3. Delete the already existing SSH rule for port 22

#### Uncomplicated Firewall (UFW)
1. Deny all incoming connections on all ports
    - `sudo ufw default deny incoming`
2. Allow all outgoing connections on all ports
    - `sudo ufw default allow outgoing`
3. Allow SSH access on port 2200
    - `sudo ufw allow 2200/tcp`
4. Allow HTTP access on port 80
    - `sudo ufw allow www`
5. Allow NTP access on port 123
    - `sudo ufw allow 123/udp`
6. Enable the firewall
    - `sudo ufw enable`
7. Check the firewall status
    - `sudo ufw status`
8. End your current SSH session then SSH into the server again using port 2200
    - If you cannot access the server you have done something wrong

### Grader user and sudo access
1. Create a new user called `grader` using `sudo adduser grader`
2. Edit the sudo file using `sudo visudo`
    - Under `root    ALL=(ALL:ALL) ALL` add a new line
            `grader    ALL=(ALL:ALL) ALL`
3. To check if this has worked switch to `grader` using `su - grader` and run `sudo -l` which should show that `grader` may run commands on `(ALL : ALL) ALL`

Resource: [How To Add and Delete Users on an Ubuntu 14.04 VPS](https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-an-ubuntu-14-04-vps)

### SSH Key for grader
1. On your local machine use `ssh-keygen` to a new key pair
2. Give the file a name such as `grader_project_key`
3. This will generate the files `~/.ssh/grader_project_key` and `~/.ssh/grader_project_key.pub`
4. Read the contents of `~/.ssh/grader_project_key.pub` using `cat ~/.ssh/grader_project_key.pub` and copy the contents
5. On your server make a new directory `.ssh` under the `grader` home directory
    - `mkdir /home/grader/.ssh`
6. Change the file permissions
    - `chown grader:grader /home/grader/.ssh`
    - `chmod 700 /home/grader/.ssh`
7. Copy the `authorized_keys` from root
    - `cp /root/.ssh/authorized_keys /home/grader/.ssh/`
8. Change the file permissions
    - `chown grader:grader /home/grader/.ssh/authorized_keys`
    - `chmod 644 /home/grader/.ssh/authorized_keys`
9. Paste the contents of `~/.ssh/grader_project_key.pub` into `/home/grader/.ssh/authorized_keys`

### Root Login
Double check that password login is disabled in `/etc/ssh/sshd_config`. The line `PasswordAuthentication` should be uncommented and followed by `no`. If you find the line `#PasswordAuthentication yes` change it to `PasswordAuthentication no`.

### UTC Timezone
Use `sudo timedatectl set-timezone UTC` to change the timezone to UTC.

### Software Installation
1. Apache HTTP Server:`sudo apt-get install apache2`
2. mod_wgsi: `sudo apt-get install libapache2-mod-wsgi`
3. PostgreSQL: `sudo apt-get install postgresql`
4. git: `sudo apt-get install git`
5. Flask: `sudo apt-get install python-psycopg2 python-flask`
6. SQLAlchemy: `sudo apt-get install python-sqlalchemy python-pip`

### Git repository
1. In `/var/www` create a new directory for the application
    - `sudo mkdir library`
2. `cd` into the new directory and clone the desired git repository into it.
3. Rename the main python file `__init__.py `

### Google oAuth
1. If you run into the issue where your app cannot find the `client_secrets.json` file go into the code and add the direct path for the file.
```
    APP_PATH = '/var/www/library/library/'
    CLIENT_ID = json.loads(open(APP_PATH + 'client_secrets.json', 'r').read())['web']['client_id']
```
2. Go to [Google Cloud Platform](https://console.cloud.google.com/) and under Credentials tab add the IP address to Authorized JavaScript origins
3. Edit `client_secrets.json` to add the IP address to javascript origins

### Secure PostgreSQL
Follow this tutorial on how to secure the database.
[How To Secure PostgreSQL on an Ubuntu VPS](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps)

### Deploy a Flask application
Follow this tutorial on how to deploy a flask application
[How To Deploy a Flask Application on an Ubuntu VPS](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
