# Raspberry pi configuration steps for using nanocal 2.0


## 1. Install and configure raspi os

- Download rapberry pi [image](https://downloads.raspberrypi.org/raspios_arm64/images/) for arm64 and write image to SD card with [Raspberry Pi Imager](https://www.raspberrypi.com/software/)
- In the case of using 7" multitouch display add to /config.txt: 
```
dtparam=i2c_vc_baudrate=50000
```  
- Switch on raspberry pi, enter locale, password, etc.  
- Activate SSH via raspi-config:
```
$ sudo raspi-config
```
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
- To use onscreen keyboard install onboard: 
```
$ sudo apt install onboard
```
and activate it with GUI. Also you can add the shortcut to the top pi panel  

## 2. Installing nanocal libraries

- Download scripts from repository:
```
$ git clone https://github.com/MelnikovAP/nanocal_pi.git
```
- Make new virtual environment for python and install DAQBoard libs, follow the [instructions](https://github.com/MelnikovAP/nanocal_pi/blob/master/README.md)  
- Go to the cloned directory and install dependencies:
```
$ pip install -r requirements.txt
```

## 3. Installing Tango server

- The following packages need to be installed for Tango server:
```
$ pip install numpy six Sphinx
$ sudo apt install mariadb-server
```
If step 2 was fully completed, one needs only:  
```
$ pip install Sphinx
$ sudo apt install mariadb-server
```
- Install [Pi Tango server](https://tango-controls.readthedocs.io/en/latest/installation/tango-on-raspberry-pi.html):
``` 
$ sudo apt install tango-db tango-starter tango-test liblog4j1.2-java  
```
- Install PyTango. The official [instructions](https://gitlab.com/tango-controls/pytango) does not work correctly. Use:
```
$ sudo apt install libboost-python-dev libtango-dev python3-tango  
```


sudo service mariadb start
wget https://gitlab.com/api/v4/projects/24125890/packages/generic/TangoSourceDistribution/9.3.5/tango-9.3.5.tar.gz
mkdir tango
cd tango
tar xzvf ../tango-9.3.5.tar.gz


sudo mariadb -u root
ALTER USER 'root'@'localhost' IDENTIFIED BY 'mypassword';
exit

./tango-9.3.5/configure --enable-java=yes --enable-mariadb=yes --enable-dbserver=yes --enable-dbcreate=yes --with-mysql-admin=root --with-mysql-admin-passwd='tonic13' --prefix=/usr/local/tango


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
