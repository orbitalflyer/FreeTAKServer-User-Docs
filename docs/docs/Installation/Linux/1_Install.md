# FreeTAK Server Installation
This guide will walk you through installing FreeTAKServer 2.0

## 0. Document Goals

- Prepare host OS
- Install FreeTAKServer
- Configure and Run FreeTAKServer
- Configure Web UI
- **TBD** ~~Install [NodeRed](https://github.com/FreeTAKTeam/FreeTAKServer-User-Docs/blob/main/docs/docs/FreeTAKHub/NodeRedinstallation.md) for FreeTAKHub~~
- **TBD** ~~Install the [Web Map](https://github.com/FreeTAKTeam/FreeTAKServer-User-Docs/blob/main/docs/docs/FreeTAKHub/WebMap/Installation.md)~~
- **TBD** ~~Install the [Video Service](https://github.com/FreeTAKTeam/FreeTAKServer-User-Docs/blob/main/docs/docs/FreeTAKHub/Video/Installation.md)~~

---
## Note: Linux Distributions

The main supported OS is Ubuntu 20.04. Due to cross-compatibility, Debian 10 and the latest Raspbian also work.

Centos/RHEL/Fedora installation instructions are provided on a best-effort volunteer basis.

---
## 1. Update OS Packages

Update OS Packages before proceeding with the installation

<details>
<summary>
Ubuntu
</summary>

```bash
sudo apt update && sudo apt upgrade
```
</details>

<details>
<summary>
RHEL
</summary>

```bash
sudo dnf update
```
</details>

---
## 2. Pre-requisite Software

<details>
<summary>
Ubuntu
</summary>

Install packages from the distro repo

```bash
sudo apt update && sudo apt install python3 && sudo apt install python3-pip
sudo apt install python3-dev python3-setuptools build-essential python3-gevent python3-lxml libcairo2-dev
```
</details>

<details>
<summary>
RHEL
</summary>

Install distro repo packages

```bash
sudo dnf group install "C Development Tools and Libraries" "Development Tools"
sudo dnf install python3 python3-pip cairo cairo-devel python3-cairo python3-cairo-devel python3-gevent python3-lxml python3-virtualenv
```

Create and activate a virtualenv for the next steps

```bash
virtualenv venv
source venv/bin/activate
```
</details>


---
## 3. Install Python Libraries

<details>
<summary>
Ubuntu
</summary>

```bash
sudo pip3 install wheel pycairo
```

Note, use of pip with sudo is not recommended and you will be warned about this!
</details>


<details>
<summary>
RHEL
</summary>


```bash
pip install wheel pycairo
```
</details>

---
## 4. Remove Old Installation
**YOU MUST DO THIS IF:**

-  Free TAK Server has been installed before
-  An upgrade fails
-  A previous installation was not completed

<details>
<summary>
Ubuntu
</summary>

```bash
sudo pip3 uninstall FreeTAKServer
sudo pip3 uninstall FreeTAKServer-UI
```
</details>

<details>
<summary>
RHEL
</summary>

```bash
deactivate
rm -rf venv/
pip uninstall FreeTAKServer FreeTAKServer-UI
```
</details>

Delete the database and log folders

```bash
sudo rm /opt/FTSDataBase.db
sudo rm -r /usr/local/lib/<your-python-version>/<dist or site>-packages/FreeTAKServer
```

---
## 5. Install FreeTAKServer
Install the FreeTAKServer  and the associated Web UI

<details>
<summary>
Ubuntu
</summary>

```bash
sudo python3 -m pip install FreeTAKServer[ui]
```
</details>

<details>
<summary>
RHEL
</summary>

```bash
pip install FreeTAKServer[ui]
```
</details>


The FreeTAKServer can be installed without the UI, however this makes the
server much more difficult to use and is probably not what you want. This can be done using the `FreeTAKServer` pip package only.

### Advanced Installations:

<details>
<summary>
Install a specific version
</summary>
To install a special version of a FreeTAKServer pip package by appending `==version-number` to the installation command.

```bash
python3 -m pip install FreeTAKServer[ui]==version-number
```

For example, if you want to install version Alpha of the requests package, you can run the following command:

```bash
python3 -m pip install FreeTAKServer[ui]==0.2.0.13
```

Old installations can be installed in the same way, if desired.

This will download and install version 0.2.0.13 of FreeTAKServer. If no version number is specified, then the latest normal release will be installed.
</details>

---
## 6. Check Installation

The pip utility allows the user to check the installation and dependency status of a package.


```bash
pip check FreeTakServer 
```

You should see `No broken requirements found.` displayed, in which case you can
proceed. Otherwise there was an issue with the pip installation and you should
investigate the pip-generated installation logs for more information.

---
## 7. Configure and Run FreeTAKServer

FTS versions older than 1.9 are not configured this way. For versions <1.9 you
will need to locate older versions of this document in the repo history.

### Start the FreeTAKServer

<details>
<summary>
Ubuntu
</summary>

```bash
sudo python3 -m FreeTAKServer.controllers.services.FTS 
```
</details>

<details>
<summary>
RHEL
</summary>

```bash
python -m FreeTAKServer.controllers.services.FTS 
```
</details>

> :memo: **Note:** Warnings about `failed to register component` are OK.
Your system will run fine with these warnings. Starting with FTS 2.0 some future
functionality is declared but not fully implemented.

On the first run, a configuration wizard will help set up the config file.

> :memo: **Note:** If the wizard does not appear, see [troubleshooting](https://freetakteam.github.io/FreeTAKServer-User-Docs/Installation/Troubleshooting/troubleshooting/).

### Using the Wizard

The default configuration option is presented in [brackets].
If the default is acceptable you can simply press enter without entering any text
to automatically use the default.

An example is provided below, your exact configuration will differ.

```css
would you like to use a yaml config file, 
 if yes you will be prompted for further configuration options [yes]:
where would you like to save the yaml config [/opt/fts/FTSConfig.yaml]: 
enter ip [127.0.0.1]: 
enter the preferred database type (MySQL is highly experimental if you're not sure leave default) [SQLite]: 
enter the preferred database path [/opt/fts/FTSDataBase.db]: 
enter the preferred main path [/usr/local/lib/python3.11/site-packages/FreeTAKServer]: 
enter the preferred log file path [/opt/fts/Logs]: 

```

> :memo: **Note:** The IP in this configuration wizard is the FTS_MAIN_IP.
This must be your EXTERNAL IP.

MySQL usage is beyond the scope of this guide, if you wish to use MySQL, it is
assumed you know how to configure database servers already. Some additional
information for MySQL installations is available in the
[FTS-UI configuration section](### MySQL Configuration).

The database and log filepaths can be anywhere that the server's host user can
access.

The main path should be the directory where pip installed FreeTAKServer.
This can be found under your python packages directory. In virtualenv installations,
it is inside the virtualenv directory.

The wizard creates the YAML configuration file under the location you selected,
the default location is /opt/FTSConfig.yaml

FreeTAKServer will then proceed start all the services.

---
## 7. FTS Configuration
### Manual FTSConfig.yaml

> :warning: **Warning:** Before modifying the YAML file, FreeTAKServer
**must be stopped!**

Use the keyboard  chord `CTRL + C` twice in the console running FTS to stop
FreeTAKServer.

A separate guide is maintained for  [manual configuration](https://github.com/FreeTAKTeam/FreeTAKServer-User-Docs/blob/main/docs/docs/Installation/PyPi/Linux/ManualConfiguration.md) of your FTS install.

### MainConfig.py
FTS sends a welcome message on client connection which is configurable. See the `FreeTAKServer/core/configuration/MainConfig.py` file to change it.

```
ConnectionMessage = f'Welcome to FreeTAKServer {version}. The Parrot is not dead. It’s just resting'
```

---
## 9. FTS-UI Configuration

Edit the `config.py` file in the `FreeTAKServer-UI` directory where it was installed by pip

<details>
<summary>Where is that?</summary>

> The FreeTAKServer-UI directory lives at the same level as the FreeTAKServer
directory that was set in the initial configuration of FTS.

>Usually that means it's inside your python packages directory.
In virtualenv installations, it is inside the python installation in side the
virtualenv directory.
</details>

### Standard UI Config

Edit the IP value to your external IP, for example:

```python
# this IP will be used to connect with the FTS API
IP = '192.168.1.100'
```

Set the web map IP address, for example:


```python
# the public IP your server is exposing
APPIP = '0.0.0.0'
# webmap IP
WEBMAPIP = "192.168.1.100"

```

In a default installation, the port should be `19023`.
Advanced users may wish to use a different port.


```python
    # Port the  UI uses to communicate with the API
    PORT = '19023'
```

### Custom Secrets Configuration
 
The following items can be updated to use your own secrets, however the values
must be updated in both the `FreeTAKServer-UI/config.py` and the
`FreeTAKServer/core/configuration/MainConfig.py` files.

The API key used by the UI to comunicate with FTS.
Generate a new system user and then set it.
~~What does this mean? Where is a user generated? What is a system user?~~

```python
app.config['APIKEY'] = 'Bearer [API_TOKEN]'
app.config['WEBSOCKETKEY'] = '[Your_Web_socket_Key]'
```
 
### MySQL Configuration

To use a MySQL database, update the URI to point to your database.

Database setup is beyond the scope of this document.

```python
SQLALCHEMY_DATABASE_URI = 'mysql://' + 'user:pass@localhost/dbname'
```
 
### Miscellaneous Parameters

Some additional parameters can be found in the `__init__.py` file in the
`FreeTAKServer-UI/app/__init__.py` directory.


These configure dashboard update frequency, ~~health pings?~~,
~~status messages?~~, and the file size limit for data packages.

```python
app.config['USERINTERVAL'] = '180000';
app.config['SERVERHEALTHINTERVAL'] = '180000';
app.config['SYSSTATUSINTERVAL'] = '600000';
app.config['DATAPACKAGESIZELIMIT'] = '15360000';
```

### 10. Start UI

In the terminal, navigate to the `FreeTAKServer-UI` directory wherever it was
installed by the pip utility.
 
Start the UI from the terminal using python:

<details>
<summary>
Ubuntu
</summary>

```bash
sudo python3 run.py
```
 </details>
 
<details>
<summary>
RHEL
</summary>
 
```bash
python run.py
```
</details>

You will see the UI server start up in your terminal, which should look similar
to that shown below.

```python
(55501) wsgi starting up on http://0.0.0.0:5000
(55501) accepted ('192.168.1.100', 49842)
```

---
## 11. Next Steps

### Create a service

To run the server without keeping the console open, a service can be created.

*See: [Service](Service.md)*

### Installation on a Separate machine
Typically the web UI  is installed on the same machine as FTS, however you can install it on a separate machine and even use it to manage several instances.

If you're installing FTS-UI on a separate server the following commands may help:

```bash
sudo pip install WTForms==2.3.3
sudo pip install SQLAlchemy==1.3.20
sudo pip install eventlet
```

The config file will also need to be updated.

```python
IP = [FTS external IP]
APPIP = [FTS-UI internal IP]
```

## 12. Check Installation

After finishing all the steps, make sure your configuration is acceptable and
server running properly.

```bash
sudo python3 -m FreeTAKServer.controllers.services.FTS
```

If you see FTS start without error you may hit `ctrl+c` twice and move onto running FTS.


your FTS is now configured


If you have setup the UI, from the Admin console send a message hello world to the client

 
## Troubleshooting

### Default Login
The default credentials for the UI are `admin` and `password`.

You are encouraged to update these quickly.


### WTForms Error Troubleshooting

In the event of a wtforms error, install it using pip:

```bash
pip3 install WTForms==2.3.3 
```

### Failed to Register Components

Warnings about `failed to register component` are OK.
Your system will run fine with these warnings. Starting with FTS 2.0 some future
functionality is declared but not fully implemented.

### UI Can't See Server

Usually this happens when you are using an OS that has a strong default firewall.

Ensure all the ports you have set in your config files are open on the firewall
for TCP traffic.

<details>
<summary>
RHEL
</summary>

```bash
firewall-cmd --add-port=19023/tcp
```
</details>
