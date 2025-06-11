# PrayerPi, a simple project using affordable technology and understandable coding to make sure you'll never miss your prayers.

This project consists of using a raspberry pi zero and other modules to make a prayer time clock with Adhan

What I used for this project : 

- Raspberry Pi Zero
- 4.2 inch e-Paper Module
- WM8960 Audio HAT
- Small speakers
- Connectors
- CQRobot Light Sensor (for later)

---


First of all we need the software and drivers for for : the Pi, the e-Paper and the audio + light sensor

For this we'll be using the Raspberry Pi Imager. I recommend the setup as it is what I used and *might not work* on others.

Pi Device : Pi Zero | OS : Raspberry Pi OS Lite ( Debian Bookworm, no desktop environment ) | Storage ( 64gb MicroSD card )

Custom Config For the Imager : 

GENERAL

- ✅ Set hostname: anameyoulike.local
- ✅ Set user and password : auseryoulike ; apasswordyoucanremember
- ✅ Configure Wifi : yourwifiname ; yourwifipassword + Wifi Country
- ✅ Set locale settings : yourtimezone | kb layout : US

SERVICES : 

- ✅ Enable SSH : Use Password AUTH

OPTIONS : 

- ✅ Enable telemetry

---

Once this is completed put the MicroSD card in it's slot, power the Pi and wait ≃ 5 minutes
From the main computer try to find the Pi's IP adress using the "ping" command like so : ping hostnameyoupicked.local
You should get a result like so : 

<img width="501" alt="image" src="https://github.com/user-attachments/assets/3fb9eab6-80ed-4a52-914f-16be12319e62" />

Now that we have the IP Adress we can start to establish SSH. It enables encrypted communication between a client ( the main computer ) and a server ( our Pi in this case ) , allowing users to execute commands, transfer files, and manage systems securely. Using the command is very simple, here's how we would proceed :

`ssh user@hostIP` but as example it would look like this `ssh nael@192.168.7.9`, if you like you can also add `-l pi` so the computer knows that the targeted computer is a RaspberryPi.
The complete command looks like this : 
- On text : `ssh nael@192.168.7.9 -l pi`.
- On computer :
 <img width="676" alt="image" src="https://github.com/user-attachments/assets/877b62cf-cb74-4d69-8b8c-d1a49e2db2da" />

---

### Now that we established a connection to our RaspberryPi we can start working !

First let's make sure we have the latest updates to work with. Let's run a simple command to ensure we have those updates : ﻿﻿
`sudo apt-get update && sudo apt upgrade -y`

Let's break down the command into four parts: 

- `sudo`
The prefix `sudo` gives us full access and makes us top priority in the processes the computer is handling. It can be compared to the administator feature on Windows and MacOs.

- `sudo apt-get update`:
This command connects to the package repositories and retrieves the latest list of available packages. It doesn't install any updates, but ensures our system has the most up-to-date information

- `sudo apt upgrade`:
This command then downloads and installs any new versions of packages that are currently installed on your system. It doesn't remove any packages or install new ones, but upgrades existing ones.

- `-y`
This extension tells the computer to proceed with the command no matter what, that the computer has our "yes".

For our Pi to complete the updates it takes approximately 10 minutes. When completed we will restart our RaspberryPi using the `sudo reboot` command. To confirm that our machine is restarting, the green light on our Pi should turn off and turn back on, 

![image](https://github.com/user-attachments/assets/aab23ea8-90a2-46f3-864a-6426b9134ea6)

at that time we will proceed to reconnect using SSH so we can continue working on the project.

---

### Let's start with enabling the SPI :

Firstly we have to enable SPI on our Pi. SPI (Serial Peripheral Interface) is a communication protocol used to connect microcontrollers to peripherals such as sensors, displays, and memory chips. On the Raspberry Pi, SPI allows the Pi to act as a master device, communicating directly with one or more SPI-enabled components.
By default, SPI is disabled on the Raspberry Pi to prevent potential conflicts with users who don't require it. It must be explicitly enabled before it can be used in projects involving SPI-based hardware like ours.

In order to do this we need to access a utility on the Pi called `raspi-config` (Raspberry Pi Software Configuration Tool) which is what would be the settings app on modern laptops. To access this utility we will use another simple command. We'll be using the same prefix `sudo` followed by the `raspi-config`, and it should look like this once we execute the command : 
<img width="1043" alt="image" src="https://github.com/user-attachments/assets/7985b6fa-dd0e-4373-a51c-8dfaa5c69eca" />

Once we have this page displayed on our main computer we will have to navigate, using arrow keys,to "Interface Options" and press `enter`. Upon entering, go to SPI and select enable, the Pi should display that the interface is enabled like so : 
<img width="566" alt="image" src="https://github.com/user-attachments/assets/3c4f8e62-dfe3-405f-a800-d55a3a5dd2a4" />

Press `enter` and then press finish and reboot your machine using `sudo reboot` once again.


### Installing drivers and modules

Now that our machine is updated software-wise and has SPI enabled , we can start worrying about the drivers and modules of the parts we are using.

For this step of the project, I will only display the commands needed to download the libraries and drivers and elaborate about those afterwards.

#### Git
`sudo apt install git` We very much need this.

#### WM8960 Audio HAT

Fetching the driver
- `git clone https://github.com/waveshare/WM8960-Audio-HAT`

Installing the Driver
- `cd WM8960-Audio-HAT` going to the directory
- `sudo ./install.sh `
- `sudo reboot`

To check if the driver is installed we simply input this command `sudo dkms status` and the output should look like so :
img here

Installing a Media Player
- `sudo apt-get install mpg123`
- `sudo mpg123 anymp3file.mp3`

You can also access a mixer to control volume and plenty more audio settings using `sudo alsamixer`

### TSL2591X Light Sensor

Enabling I2C
- `sudo raspi-config` Going into system configuration -> Interface Options -> i2c -> Yes (to start the driver)
- `sudo reboot` To make sure driver runs on startup


#### e-Paper Module : Drivers, Modules, Libraries

#### Python

Install the function library
- `sudo apt-get install python3-pip`
- `sudo apt-get install python3-pil`
- `sudo apt-get install python3-numpy`
- `sudo pip3 install spidev`

#### C

Install lg library
- `wget https://github.com/joan2937/lg/archive/master.zip` Fetching the library
- `unzip master.zip`
- `cd lg-master`
- `make`
- `sudo make install` Similar to opening a "setup.exe" file on windows or "setup.pkg" on macos and linux

Install gpio library
- `cd` Going back to the main directory
- `sudo apt install gpiod libgpiod-dev` Fetching and install the gpio library as dev so we can have full access to the library

Install BCM2835
- `cd`
- `wget http://www.airspayce.com/mikem/bcm2835/bcm2835-1.71.tar.gz`
- `tar zxvf bcm2835-1.71.tar.gz` Opening the tar.gz file
- `cd bcm2835-1.71/` Going to the directory bcm2835-1.71/
- `sudo ./configure && sudo make && sudo make check && sudo make install` Configuring and making the install on the Pi

Install WiringPi
- `cd`
- `sudo apt-get install wiringpi` Fetching and installing WiringPi for older Pi systems (before 2019). For Raspberry Pi systems after May 2019 (earlier than before, you may not need to execute), you may need to upgrade:
- `wget https://project-downloads.drogon.net/wiringpi-latest.deb` Fetching the library
- `sudo dpkg -i wiringpi-latest.deb` Opening and configuring the wiringpi
- `gpio -v` Checking the installation
 Version 2.52 should appear.

Download the demo
- `cd`
- `wget https://files.waveshare.com/upload/7/71/E-Paper_code.zip` Fetching the demo
- `unzip E-Paper_code.zip -d e-Paper`
- `cd e-Paper/RaspberryPi_JetsonNano/`
 Now at e-Paper/RaspberryPi_JetsonNano
- `cd c`
- `sudo make clean` Removing files we dont need
- `sudo make -j4 EPD=epd4in2V2` Making the instalation for the 4.2 inch model

Run the demo
- `sudo ./epd` and the e-Paper module should display a demo


WARNING : THIS PROJECT IS NOT COMPLETED I AM STILL WORKING ON IT THAT'S WHY MOST OF THE PROJECT IS NOT HERE YET. I AM PREPARING FOR THE PROJECT. AS A STUDENT I DO NOT HAVE A LOT OF TIME I WORK ON THIS WITH THE TIME I HAVE.
