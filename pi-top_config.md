# Pi-top configuration steps for using nanocal 2.0


## 1. Install and configure raspi os

- Download pi-top [image]([https://downloads.raspberrypi.org/raspios_arm64/images/](https://www.pi-top.com/resources/download-os) 
- Switch on pi-top  
- Activate SSH with pi-top screen and buttons
- Find ip address on pi=top display
- Now you can connect via ssh: ssh -X pi@{ip_address}. default password is "pi-top"
- Set the static ip address via: 
```
$ sudo nano /etc/dhcpcd.conf
```
For ESRF the settings will be: 
```
interface eth0
static ip_address=160.103.33.50/24
static routers=160.103.33.99
static domain_name_servers=160.103.209.9 160.103.208.9
```
- Maybe one needs to change time and date:  
```
$ sudo date -s 'YYYY-MM-DD HH:MM:SS'
```
- Update all the packages:
```
$ sudo apt update  
$ sudo apt upgrade
```
- Change the password:
```
passwd
```
- Change hostname:
```
sudo nano /etc/hostname
sudo nano /etc/hosts
```

## 2. Installing nanocal libraries

- Make new virtual environment for python and install DAQBoard libs, follow the [instructions](https://github.com/MelnikovAP/nanocal_pi/blob/main/README.md) 
- Download scripts from repository:
```
$ git clone https://github.com/MelnikovAP/nanocal_pi.git
```
- Go to the cloned directory and install dependencies:
```
$ pip install -r requirements.txt
```

## 3. Installing Tango server

- The following packages need to be installed for Tango server:
```
$ pip install numpy six Sphinx
$ sudo apt install g++ mariadb-server libmariadb-dev zlib1g-dev libomniorb4-dev libcos4-dev omniidl libzmq3-dev make
```
If step 2 was fully completed, one needs only:  
```
$ pip install Sphinx
$ sudo apt install mariadb-server libmariadb-dev zlib1g-dev libomniorb4-dev libcos4-dev omniidl libzmq3-dev
$ sudo service mariadb start
```
- Install [Pi Tango server](https://tango-controls.readthedocs.io/en/latest/installation/tango-on-raspberry-pi.html):
``` 
$ sudo apt install tango-db
```
enter host and port (raspberrypi:10000); configure db -> Yes; password - empty
``` 
$ sudo apt install tango-starter tango-test liblog4j1.2-java  
```
- Install PyTango. The official [instructions](https://gitlab.com/tango-controls/pytango) does not work correctly. Use:
```
$ sudo apt install libboost-python-dev libtango-dev python3-tango
```
- Graphic tools (Jive, Astor,…) installation. Download the latest version of libtango-java librairies on [picaa](https://people.debian.org/~picca/) and install it:
```
wget -c https://people.debian.org/~picca/libtango-java_XX_version.deb\
sudo dpkg -i ./libtango-java_XX_version.deb
```
if there is a problem with the last command, use 
```
sudo apt install tango-starter tango-test liblog4j1.2-java
sudo apt --fix-broken install
sudo dpkg -i ./libtango-java_XX_version.deb
```  
Install java 8 and set is as default java (Tango graphic tools are working only with java 8!!):
```
sudo apt-get install openjdk-8-jdk
sudo update-alternatives --config java
java -version
```
Install NanoControl server to Tango:
```
jive&
```
In Jive Tools -> Server wizard -> Server name: NanoControl; Instance name: NanoControl  
In console run server from /nanical_pi:
```
python nanocontrol_tango.py NanoControl
```
in Jive Server wizard continue: Declare -> NanoControl/NanoControl/1 



- Install supervisor for Tango server auto-start:  
```
sudo apt install supervisor
supervisord --version
```
- Configure Supervisor. Edit /etc/supervisor/supervisord.conf. Add the following:  
```
[inet_http_server]
port = 160.103.33.50:9001
username = pi
password = tonic13

[program:nanocontrol_tango]
directory = /home/pi/nanocal_pi
command = python nanocontrol_tango.py NanoControl
stdout_logfile = /home/pi/nanocal_pi/logs/supervisor_nanocontrol_tango.log
stderr_logfile= /home/pi/nanocal_pi/logs/supervisor_nanocontrol_tango.err
autostart = true
startsecs = 5
autorestart = true
user = pi

[program:nanocontrol_http]
directory = /home/pi/nanocal_pi
command = python -m http.server
stdout_logfile = /home/pi/nanocal_pi/logs/supervisor_nanocontrol_http.log
stderr_logfile= /home/pi/nanocal_pi/logs/supervisor_nanocontrol_http.err
autostart = true
startsecs = 5
autorestart = true
user = pi
```
```
sudo systemctl enable supervisor --now
sudo systemctl status supervisor
sudo systemctl restart supervisor
sudo supervisorctl reload
```

## Optional

- Configure git:
```
$ git config --global user.name "username"
$ git config --global user.email "email"
```

- If mistake with acces to USB:
https://askubuntu.com/questions/978552/how-do-i-make-libusb-work-as-non-root
$ sudo nano /etc/udev/rules.d/90-usbpermission.rules
add there: SUBSYSTEM==“usb”,GROUP=“users”,MODE=“0666”

## For testing Tango on Windows
- Install Java from [here](https://www.java.com/en/download/)  
- Install Tango from [here](https://www.tango-controls.org/downloads/)  
- Set enviroment variable TANGO_HOST='raspberrypi:10000'  
- Add to path C:\Program Files\tango\bin  
```
pip install numpy six Sphinx pytango 
```



???
https://anaconda.org/tango-controls/repo

think about auto installation on raspberry using script



Installing Java 8 on Debian (to launch jive):
https://linuxize.com/post/install-java-on-debian-10/
