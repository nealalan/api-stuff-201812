# [nealalan.github.io](https://nealalan.github.io)/[api-stuff-201812](https://nealalan.github.io/api-stuff-201812)

## Python Full Stack Playing Around
### Goals
- not to hose up the system i'm on
- **do it live d\*mn it, i'll do it live** (that ref makes me laugh every time)
- but really, I want to test out these API's on a domain/subdomain, not just on localhost
- i get no enjoyment doing things on localhost
- take the time to use virtualenv so I don't have to use **$ terraform taint** again
- Note: Currently on VERSION="18.04.1 LTS (Bionic Beaver)"

### Setup
- Using a page from [FullStackPython blog](https://www.fullstackpython.com/blog/python-3-flask-gunicorn-ubuntu-1804-bionic-beaver.html)

```bash
$ python3 --version
$ which python3
$ sudo apt-get install python3-dev python3-pip python3-virtualenv
# Note for some reason we have to separately install python3-venv
$ sudo apt-get install python3-venv
```

The following NEW packages will be installed:
- binutils binutils-common binutils-x86-64-linux-gnu build-essential cpp cpp-7 dh-python dpkg-dev fakeroot g++ g++-7
  gcc gcc-7 gcc-7-base libalgorithm-diff-perl libalgorithm-diff-xs-perl libalgorithm-merge-perl libasan4 libatomic1
  libbinutils libc-dev-bin libc6-dev libcc1-0 libcilkrts5 libdpkg-perl libexpat1-dev libfakeroot
  libfile-fcntllock-perl libgcc-7-dev libgomp1 libisl19 libitm1 liblsan0 libmpc3 libmpx2 libpython3-dev
  libpython3.6-dev libquadmath0 libstdc++-7-dev libtsan0 libubsan0 linux-libc-dev make manpages-dev python-pip-whl
  python3-crypto python3-dev python3-distutils python3-keyring python3-keyrings.alt python3-lib2to3 python3-pip
  python3-secretstorage python3-setuptools python3-virtualenv python3-wheel python3-xdg python3.6-dev
The following NEW packages will be installed:
- python3-venv python3.6-venv
We installed virtualenv and pip to handle our application dependencies. 

```bash
$ pip3 install --upgrade pip setuptools
$ mkdir venvs Projects 
$ python3 -m venv venvs/flask1804
$ source ~/venvs/flask1804/bin/activate
```

We are now safe to install within the virtual environments /bin and /lib w/out hosing up PROD

![](https://github.com/nealalan/api-stuff-201812/blob/master/images/Screen%20Shot%202018-12-10%20at%2011.17.07%20PM.jpg?raw=true)

### TOOLS in Virtual Env Project
- So from now on, our primary package manager will be PIP and wheel?! Keep that in mind...
- [Flask](http://flask.pocoo.org/docs/1.0/) = Python micro framework for building web applications.
- [Gunnison](https://gunicorn.org/) -  'Green Unicorn' is a Python WSGI HTTP Server for UNIX

```bash
$ pip install --upgrade pip
$ pip install wheel
$ pip install flask gunicorn
```

- Now lets test out the env. We are going to create a file from the command line using the tee command...

```bash
$ cd ~/Projects
$ mkdir flask1804
$ cd flask1804
$ tee __init__.py << END
> from flask import Flask, Response
>
>
> app = Flask(__name__)
>
> @app.route("/")
> def index():
>     return Response("It works!"), 200
>
> if __name__ == "__main__":
>     app.run(debug=True)
> END
$ cat __init__.py
```

A sloppy but effective way to create our script. But it will work with python flask:

```bash
# Note: you're in your virtual env right?
$ python __init__.py
```
And it worked... but not so lucky with gunicorn. :( I keep getting a wierd error.

![](https://github.com/nealalan/api-stuff-201812/blob/master/images/Screen%20Shot%202018-12-12%20at%2012.10.53%20AM.jpg?raw=true)

### Now, on to [gunicorn.org](https://gunicorn.org/)
Apparently it is this simple. I tried it. Same error. I went into the code and thought, what did I do? 

![](https://github.com/nealalan/api-stuff-201812/blob/master/images/Screen%20Shot%202018-12-12%20at%2012.06.32%20AM.jpg?raw=true)

SPACING. I fixed the spacing and wham! Apparently gunicorn is really picky about code indenting! 

![](https://github.com/nealalan/api-stuff-201812/blob/master/images/Screen%20Shot%202018-12-12%20at%2012.20.59%20AM.jpg?raw=true)

When I $ cat the file, it's a bit less indented. Maybe this is the reason, maybe not. But it.... worked.

![](https://github.com/nealalan/api-stuff-201812/blob/master/images/Screen%20Shot%202018-12-12%20at%2012.16.34%20AM.jpg?raw=true)

### What is this gunicorn?
- Web Server Gateway Interface (WSGI) - a simple calling convention for web servers to forward requests to web applications or frameworks written in the Python 
- The more I looked into Gunicorn, it seems it's pretty meatless. Not a lot to it and it has to be used with other tool such as Django.
- Setting up gunicorn to work from the internet appears to be a challenge. I haven't found any comprenensive labs, but found [a doc on it](https://media.readthedocs.org/pdf/gunicorn-docs/latest/gunicorn-docs.pdf).
- But after a few hours of reading, it really doesn't tell you squat about how it's working and gives an nginx.conf that has inadequate documentation/comments. 

## [Deploying A Flask RESTPlus API to AWS EC2](https://medium.com/@isaacndungu/deploying-a-flask-restplus-api-to-aws-ec2-5061450fdc56)
- Phase 1: setup your AWS Ubuntu Instance. (Warning sign 1 - Ubuntu 16 vs 18. We will see.)
- Phase 2: automatic deployment?

```bash
# Step 1: Clone the automation scripts
$ git clone -b deploy-automation https://github.com/indungu/devops-08.git setup/
# Step 2: Run the deployment script
# on second thought lets look at it first
```

So this script would have hosed my webserver. I'll scrape the deploy-backend.sh and pull what I need from it.

```bash
# new venv
$ python3 -m venv venvs/api
$ source ~/venvs/api/bin/activate
#
# Installing App dependencies and Env Variables
$ git clone -b develop https://github.com/indungu/yummy-rest.git ~/Projectrs/yummy-rest
$ pip install -r ~/Projects/yummy-rest/requirements.txt
```

Naturally, a bunch of the installs failed. Damnit. Every. Lab. Ever! Welcome to IT, right? It appears the install just failed to build wheels for everything using.

```bash
$ pip check
# everything appears to be okay... but just to be sure:
$ sudo apt update
$ sudo apt upgrade
```

Now we need to create an environmental variables file. I think this is where everything here might fall apart.

```bash
$ sudo cat > ~/Projects/.env << EOF
    export DATABASE_URL="postgres://budufkitteymek:095f0029056c313190744c68ca69d19a2e315483bc41e059b40d6d9fdccf2599@ec2-107-22-229-213.compute-1.amazonaws.com:5432/d2r8p5ai580nqq"
    export APP_CONFIG="production"
    export SECRET_KEY="mYd3rTyL!tTl#sEcR3t"
    export FLASK_APP=run.py
EOF
$ source ~/Projects/.env
```

### Configure NGINX
Create a new nginx.conf for this project... not clobering what I already have installed.

```bash
$ sudo bash -c 'cat <<EOF > /etc/nginx/sites-available/fire.neonaluminum.com
server {
        listen 80;
        listen [::]:80;
        server_name fire.neonaluminum.com;
        location / {
                # reverse proxy and serve the app
                # running on the localhost:8000
                proxy_pass http://127.0.0.1:8000/;
                proxy_set_header HOST \$host;
                proxy_set_header X-Forwarded-Proto \$scheme;
                proxy_set_header X-Real-IP \$remote_addr;
                proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        }
}
EOF'
$ sudo ln -s /etc/nginx/sites-available/fire.neonaluminum.com /etc/nginx/sites-enabled/
```

Now we have NGINX all configured, we can go ahead and restart and verify it.

```bash
$ sudo systemctl restart nginx
$ sudo nginx -t
```

Sometimes I have a hell of a time getting this to work. NGINX won't die. So I have to

```bash
$ ps aux | grep 'nginx'
# find the PID for nginx
$ sudo kill <pid>
```

Run certbot to reginster the domain name you're using if you're using one. I needed to expand my certs for my server. I was using https://fire.neonaluminum.com 

```bash
$ sudo certbot --authenticator standalone --installer nginx -d nealalan.com -d www.nealalan.com -d neonaluminum.com -d www.neonaluminum.com -d fire.neonaluminum.com --post-hook 'sudo service nginx start' -m neal@nealalan.com --agree-tos --eff-email --redirect -q --expand
```

### Launch the yummy-rest service
Add a launch script...

```bash
$ sudo cat > /home/ubuntu/Projects/launch.sh <<EOF
#!/bin/bash
cd ~/Projects/yummy-rest
source ~/Projects/.env
source ~/venvs/api/bin/activate
gunicorn app:APP -D
$ sudo chmod 744 /home/ubuntu/Projects/launch.sh
```

Configuration startup service creation...
```bash
$ sudo bash -c 'cat > /etc/systemd/system/yummy-rest.service <<EOF
[Unit]
Description=yummy-rest startup service
After=network.target
[Service]
User=ubuntu
ExecStart=/bin/bash /home/ubuntu/Projects/launch.sh
[Install]
WantedBy=multi-user.target
EOF'
$ sudo chmod 664 /etc/systemd/system/yummy-rest.service
```

Start the yummy-rest api

```bash
$ sudo systemctl daemon-reload
$ sudo systemctl enable yummy-rest.service
$ sudo systemctl start yummy-rest.service
$ sudo service yummy-rest status
$ sudo ~/Projects/launch.sh
```

Somehow I got it to work...
- One issue I ran into was Chrome would not load the page because of cross-site scripting errors.
- Clear the cookies!

![](https://github.com/nealalan/api-stuff-201812/blob/master/images/Screen%20Shot%202018-12-15%20at%208.16.25%20PM.jpg?raw=true)

### I need a DB to use the API...

```bash
$ sudo apt update
$ sudo apt -y upgrade
$ cd ~/Projects
$ source ~/venvs/api/bin/activate
$ sudo apt install postgresql-client-common
$ sudo apt install postgresql
```

Output from the install was...

```bash
$ /usr/lib/postgresql/10/bin/pg_ctl -D /var/lib/postgresql/10/main -l logfile start
```
Now, using postgres...
```bash
$ sudo -u postgres psql
```
![](https://github.com/nealalan/api-stuff-201812/blob/master/images/Screen%20Shot%202018-12-15%20at%208.38.06%20PM.jpg?raw=true)

That let me in but I don't know where the SQL database is. So I did a locate:

```bash
$ locate yummy_rest_db
```

![](https://github.com/nealalan/api-stuff-201812/blob/master/images/Screen%20Shot%202018-12-15%20at%208.43.42%20PM.jpg?raw=true)

### [Database setup](https://github.com/indungu/yummy-rest#database-setup)
Coming back to it the next day with fresh eyes, I checked to see if postgres was running. It is. 

![](https://github.com/nealalan/api-stuff-201812/blob/master/images/Screen%20Shot%202018-12-16%20at%2013.41.02.jpg?raw=true)

I seem to be having an issue connecting to the database. I found a [stackoverflow post](https://stackoverflow.com/questions/16973018/createuser-could-not-connect-to-database-postgres-fatal-role-tom-does-not-e) about this issue.

```bash
# CREATE A USER
$ sudo -u postgres createuser ubuntu
# CREATE A DB
$ sudo -u postgres createdb -O ubuntu yummy-rest-db
# ENTER POSTGRES DATABASE yummy-rest-db
$ psql yummy-rest-db
```

![](https://github.com/nealalan/api-stuff-201812/blob/master/images/Screen%20Shot%202018-12-16%20at%206.51.11%20PM.jpg?raw=true)

I still need to create a password for the user postgres
```bash
$ sudo nano /etc/postgresql/10/main/pg_hba.conf
# Change the "host  all  all  127.0.0.1/32  md5"
#    line to "host  all  all  127.0.0.1/32  trust"
$ sudo service postgresql restart
# log into psql and set the postgres user password
$ psql -h 127.0.0.1 -U postgres
> \password
# change the line back!!!!!!
$ sudo nano /etc/postgresql/10/main/pg_hba.confq
```
[PostgreSQL 10.6 Documentation](https://www.postgresql.org/docs/10/index.html) - Figuring out how to use postgres...
```bash
# List of databases
> \l
# List of tables, views and sequences
> \dS
# List of users
> \du
# Connect from the postgres db to the yummy-rest-db
> \c yummy-rest-db
# quit
> \q
```

![](https://github.com/nealalan/api-stuff-201812/blob/master/images/Screen%20Shot%202018-12-16%20at%206.51.11%20PM.jpg?raw=true

Add users
```user
> CREATE USER new_username;
> ALTER USER new_username SUPERUSER CREATEDB;
```

### Front end stuff
```bash
# Install NodeJS and NPM
$ sudo curl -sL https://deb.nodesource.com/setup_8.x -o nodesource_setup.sh
$ sudo bash nodesource_setup.sh
$ sudo apt-get install -y nodejs
# Clone App Repo
$ git clone -b develop https://github.com/indungu/yummy-react.git ~/Projects/yummy-react
# Setup app
$ sudo npm install --global yarn
$ sudo yarn install
$ sudo yarn build production
```
Create an NGINX config (I actually added this to my existing fire.neonaluminum.com config and the same server block with "location /api/")
```bash
sudo bash -c 'cat > /etc/nginx/sites-available/yummyreact << EOF
server {
        listen 80 default_server;
        listen [::]:80 default_server;
        server_name _;
        location / {
                # reverse proxy and serve the app
                # running on the localhost:3000
                proxy_pass http://127.0.0.1:3000/;
                proxy_set_header HOST \$host;
                proxy_set_header X-Forwarded-Proto \$scheme;
                proxy_set_header X-Real-IP \$remote_addr;
                proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        }
}'
```

Launch the yummy frontend service...

```bash
$ sudo bash -c 'cat > ~/launch.sh << EOF
#!/bin/bash
cd ~/Projects/yummy-react
yarn start
'EOF
$ sudo chmod +x ~/launch.sh
```

```bash
$ sudo bash -c 'cat > /etc/systemd/system/yummy.service <<EOF
[Unit]
Description=yummy-react launch service
After=network.target
[Service]
User=ubuntu
ExecStart=/bin/bash ~/Projects/launch-frontend.sh
Restart=always
[Install]
WantedBy=multi-user.target
'EOF
```

```bash
$ sudo chmod 664 /etc/systemd/system/yummy.service
$ sudo systemctl daemon-reload
$ sudo systemctl enable yummy.service
$ sudo systemctl start yummy.service
```

I ended up changing this over to ozark.neonaluminum because I wanted to seperate out the yummy-rest from the yummy-react. Once I did this I finally figured out how to launch the site.

```bash
$ sudo yarn install
$ sudo yarn start 
```

To start a build

```bash
$ sudo yarn build
$ sudo yarn global add serve
$ sudo serve -s build -l 3000
```

The issue is, it's not running in the background.

```bash
$ sudo yarn global add deploy
```

## **TOTAL CRAP** [Secure REST API in NodeJS](https://www.toptal.com/nodejs/secure-rest-api-in-nodejs)

1. [Install NodeJS on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-18-04)

At this time, Node v8.15.0 is installed. to install the latest, the instructues will tell you how. 

```bash
$ sudo apt update && sudo apt install -y nodejs
# install node package manager (npm)
$ sudo apt install npm
# see the version
$ nodejs -v
```

2. Install MongoDB

```bash
$ sudo apt install mongodb
```
  - mongo-tools/bionic 3.6.3-0ubuntu1 amd64
    collection of tools for administering MongoDB servers
  - mongodb/bionic 1:3.6.3-0ubuntu1 amd64
    object/document-oriented database (metapackage)
  - mongodb-clients/bionic 1:3.6.3-0ubuntu1 amd64
    object/document-oriented database (client apps)
  - mongodb-server/bionic 1:3.6.3-0ubuntu1 all
    object/document-oriented database (managed server package)
  - mongodb-server-core/bionic 1:3.6.3-0ubuntu1 amd64
    object/document-oriented database (server binaries package)

3. Create a folder that we’ll be using for our project and create the package.json file for the project.

```bash
$ cd Projects
$ mkdir simple-rest-api
$ cd simple-rest-api
$ npm init
```
For this init I just entered through defaults and blank values.

Note: I received an error and ran
```bash
$ sudo chown -R ubuntu:ubuntu /home/ubuntu/.config
$ npm update
$ npm init
```

Edit your package.json file with
```json
{
 "name": "rest-api-tutorial",
 "version": "1.0.0",
 "description": "",
 "main": "index.js",
 "scripts": {
   "test": "echo \"Error: no test specified\" && exit 1"
 },
 "repository": {
   "type": "git",
   "url": "git+https://github.com/makinhs/rest-api-tutorial.git"
 },
 "author": "",
 "license": "ISC",
 "bugs": {
   "url": "https://github.com/makinhs/rest-api-tutorial/issues"
 },
 "homepage": "https://github.com/makinhs/rest-api-tutorial#readme",
 "dependencies": {
   "body-parser": "1.7.0",
   "express": "^4.8.7",
   "jsonwebtoken": "^7.3.0",
   "moment": "^2.17.1",
   "moment-timezone": "^0.5.13",
   "mongoose": "^5.1.1",
   "node-uuid": "^1.4.8",
   "swagger-ui-express": "^2.0.13",
   "sync-request": "^4.0.2"
 }
}
```

4. [Express](https://expressjs.com/): Fast, unopinionated, minimalist web framework for Node.js
```bash
$ npm install express --save
```
I ran into some vulnerabilities so I forced them to be fixed
```bash
$ npm audit fix --force
# TRY RUNNING AGAIN
$ npm install express --save
```

5. [mongoose](https://mongoosejs.com/) elegant mongodb object modeling for node.js
```bash
$ npm install mongoose
```

6. The whole fucking tutorial just fell apart. Worthless. Another couple hours of wasted frustration.

7. Tried this, but still not sure how to start the service.
```bash
$ git clone https://github.com/makinhs/rest-api-tutorial.git
$ npm audit fix --force
$ npm install

```

## [Set Up a Node.js Application for Production](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-node-js-application-for-production-on-ubuntu-16-04)

SCOPE
- Node.js applications can be run at the command line, 
- but we'll focus on running them as a service.
- they will automatically restart on reboot or failure, and can safely be used in a production environment.
- server will run a Node.js application managed by PM2
- provide users with secure access to the application through an Nginx reverse proxy
- Nginx server will offer HTTPS, using a free certificate provided by Let's Encrypt

ASSUMPTIONS
- Ubuntu 16.04 server, configured with a non-root user with sudo privileges
- domain name pointed at your server's public IP
- Nginx configured with SSL using Let's Encrypt
- you've completed the prerequisites you will have a server serving the default Nginx placeholder page at https://example.com/

##### 1. Install Node.js
See above section

##### 2. Create Node.js Application

- First, create and open your Node.js application for editing. 
- For this tutorial, we will use nano to edit a sample application called hello.js.
- Insert the following code into the file. If you want to, you may replace the highlighted port, 8080, in both locations (be sure to use a non-admin port, i.e. 1024 or greater).

```js
#!/usr/bin/env nodejs
var http = require('http');
http.createServer(function (req, res) {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Hello World\n');
}).listen(8080, 'localhost');
console.log('Server running at http://localhost:8080/');
```

- This Node.js application simply listens on the specified address (localhost) and port (8080), and returns "Hello World" with a 200 HTTP success code. 
- Since we're listening on localhost, remote clients won't be able to connect to our application.

##### 3. Test application

```bash
$ chmod +x ./hello.js
$ ./hello.js
```

In another window...
```bash
curl http://localhost:8080
```

Once you're sure it's working, kill the application (if you haven't already) by pressing Ctrl+C.

##### 4. Install [PM2](https://pm2.io)

- PM2 is a process manager for Node.js applications. 
- PM2 provides an easy way to manage and daemonize applications (run them in the background as a service).
- Use npm, a package manager for Node modules that installs with Node.js, to install PM2 on our server.

```bash
$ sudo npm install -g pm2
```

##### 5. Start Application with PM2

The first thing you will want to do is use the pm2 start command to run your application, hello.js, in the background.

```bash
$ pm2 start hello.js
```
Start and Daemonize any application:
```bash          
$ pm2 start app.js
```
Load Balance 4 instances of api.js:
```bash
$ pm2 start api.js -i 4
```
Monitor in production:
```bash
$ pm2 monitor
```
Make pm2 auto-boot at server restart:
```bash
$ pm2 startup
```
Also...
```bash
$ pm2 stop app_name_or_id
#
$ pm2 restart app_name_or_id
#
$ pm2 list
#
$ pm2 info example
#
$ pm2 monit
# Freeze a process list on reboot via:
$ pm2 save
# Remove init script via:
$ pm2 unstartup systemd
```

![](https://github.com/nealalan/api-stuff-201812/blob/master/images/Screen%20Shot%202018-12-31%20at%2012.00.45%20AM.jpg?raw=true)

##### 6. Add pm2 to systemd to launch on server boots

The startup subcommand generates and configures a startup script to launch PM2 and its managed processes on server boots

```bash
$ pm2 startup systemd
```

![](https://github.com/nealalan/api-stuff-201812/blob/master/images/Screen%20Shot%202018-12-31%20at%2012.06.31%20AM.jpg?raw=true)

The last line of the resulting output will include a command that you must run with superuser privileges. Run the command

```bash
$ sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u ubuntu --hp /home/ubuntu
```

![](https://github.com/nealalan/api-stuff-201812/blob/master/images/Screen%20Shot%202018-12-31%20at%2012.09.34%20AM.jpg?raw=true)

##### 7. Set Up Nginx as a Reverse Proxy Server
In a file within sites-available add the following location block

```bash
location /app2 {
        proxy_pass http://localhost:8081;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
```

##### 8. Implement
Once you are done...

```bash
$ sudo nginx -t
$ sudo systemctl restart nginx
```
If nginx restarts fine:
- Add ports 8080 and 8430 to the Network ACL and the Public Subnet Security Group.
- If the web server will not accept the traffic, restart it.

## sometime...
```bash
echo "# api-stuff-201812" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:nealalan/api-stuff-201812.git
git push -u origin master
```

[[edit](https://github.com/nealalan/api-stuff-201812/edit/master/README.md)]
