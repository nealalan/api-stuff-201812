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
