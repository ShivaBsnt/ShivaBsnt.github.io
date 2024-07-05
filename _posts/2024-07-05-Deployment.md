# Install Postgres on Server
[How To Install and Use PostgreSQL on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04)
```console
sudo apt update
sudo apt install postgresql postgresql-contrib
```

```console
sudo systemctl start postgresql.service
```

# Install Git Nginx and other Tools
```console
sudo apt-get install -y nginx git make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev xz-utils python-setuptools         
```
# Setting Up Virtual Env and Install Server Dependency
```console
sudo apt-get install python3-venv libcairo2
sudo apt-get install build-essential libssl-dev libffi-dev
sudo apt-get install python3-pip python3-setuptools python3-wheel
sudo apt-get install libpango-1.0-0 libpangocairo-1.0-0 libgdk-pixbuf2.0-0 shared-mime-info
sudo apt-get install python3-dev python3-cffi
python3 -m venv env
```

# Activate Virtual Env
```console
source env/bin/activate
```
