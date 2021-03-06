Who is this designed for?
------------------------------------
This script (setup_dev_env_in_wsl2.sh) can be used by any dev, but is specifically created for a VIVO dev that may also want or have the need to use that env for other projects.

It is designed to be used the WSL2 on Windows 10 Build 2004 or higher and with the Ubuntu 18.04 Distro.

The PowerShell script is to assist those that currently do not have WSL2 installed.  You should reboot after running.

Sets up the following within WSL2
------------------------------------
Azure CLI\
dotNet SDK v2.1\
dotNet SDK v3.1\
Mono Complete v6.12\
NodeJS v12.19\
NPM v6.14\
PowerShell Core 7\
Docker CE v19.03\
kubectl\
Python v3.8\
Pip3 v9.0.1\
docker-compose (via Pip3)\
Git v2.28 (git-all)\
OpenJDK 11 LTS (JDK and JRE)\
Maven v3.6\
Tomcat v9\
Solr v8.6.3\
smtp4dev (as a docker container) - the "fake" smtp email server for development and testing\
MariaDB v10.5\
OpenBSD SSH Server

Adds the Repos
-------------------------------------
Microsoft Azure Repos (and Key)\
Microsoft dotNet Core Repo (and Key)\
Mono Repo (and Key)\
NodeJS and NPM Repo from Nodesource\
Microsoft Ubuntu Repo (and Key)\
Docker Repo (and Key)\
Google Cloud Kubectl Repo (and Key)\
MariaDB Repo (and Key)\
"Ubuntu Git Maintainers" team ppa:git-core/ppa\
Python versions packaged for Ubuntu ppa:deadsnakes/ppa

(Note: By adding the above repos apt-get is able to keep those items up to date.)

What the script does beyond package installs...
--------------------------------------
1. Creates the keys for the SSH Server
2. Creates the key pair for the WSL account
3. Creates a tomcat instance called "scholars"
4. Sets up "vivocore" in Solr
5. Appends to .profile the service starts
6. Appends "sudo apt-get update && sudo apt-get upgrade -y && sudo apt-get autoremove -y" to keep the system updated.
7. Turns off Microsoft dotNet Telemetry for additional privacy
8. Clones from GitHub: VIVO, Vitro, VIVO-Languages, Vitro-Languages, and VIVO Sample Data

```
DOTNET_CLI_TELEMETRY_OPTOUT=1
sudo service ssh start
sudo service docker start
docker run -d -p 3000:80 -p 25:25 rnwood/smtp4dev:v3
sudo service solr start
sudo service mariadb start
~/scholars/bin/startup.sh
sudo apt-get update && sudo apt-get upgrade -y && sudo apt-get autoremove -y
```

Other
--------------------------------------
You may want to add this to your /etc/security/limits.conf

```
solr    soft    nofile  65000
solr    soft    nproc   65000
solr    hard    nofile  65000
solr    hard    nproc   65000
```

Add to ~/scholars/conf/tomcat-users.xml (and make sure you change that password)

```
#paste inside <tomcat-users> </tomcat-users> tags

<!-- user manager can access only manager section -->
<role rolename="manager-gui" />
<user username="manager" password="notagoodpassword" roles="manager-gui" />

<!-- user admin can access manager and admin section both -->
<role rolename="admin-gui" />
<user username="admin" password="notagoodpassword" roles="manager-gui,admin-gui" />
```

To update the paths in the VIVO settings.xml file within vivo_files 

```
sed -i 's/wsl_username/'${USER}'/g' settings.xml
```
