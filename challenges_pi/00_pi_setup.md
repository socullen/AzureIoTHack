# Set up your environment

To work properly with all the different moving bits and parts we want to connect in this Hackathon, we will have to do quite a bit of setting up. Make sure you have everything on hand that you need:
- your local machine (any computer or set up a virtual machine if you do not feel like installing any additional stuff on your machine)
- an Azure subscription (go to https://azure.microsoft.com/en-us/free/ and create a new free tier account)
- a Raspberry Pi 4 with charging cable and micro SD
- a Sense HAT, which will collect all the data 
- Optional but preferred: 
    - An SD Adapter depending on whether or not your device has an SD or micro SD slot - with preinstalled Raspberry Pi OS on your micro SD card, this is not needed but still preferred
    - Desktop/Monitor (with micro hdmi adapter), keyboard, mouse <br>
    <br>
    <br>

## Your Azure subscription
1. Set up your Azure Cloud Shell. To do so go to the Azure portal. You can find it under [portal.azure.com](https://portal.azure.com). Log into Azure with your account. 
1. You might be seeing the wrong subscription right now. Select *Directories + subscriptions* - the second icon right from the search bar in the upper menue. Switch to the correct directory if necessary and under *Default subscription filter* only tag the subscription you want to work on.
![Image of the upper bar in the Azure portal with focus on the Directories + subscriptions icon](/images/00portalsub.png)
    > Have your portal in English - the translation is not helpful for technical tasks.
1. Now that you are in the right place. Select the *Azure Clound Shell* - the icon right next to the search bar in the upper menue. You will be prompted to set up a storage account. Make sure again to use the correct subscription in the dropdown menue and select *Create storage*. A PowerShell will opern in your browser. In the dropdown you can also switch to Bash.
![Image of the upper bar in the Azure portal with focus on the Cloud Shell icon](/images/00portalshell.png)
    This created a new resource group within your subscription. A resource group is a logical container for all your resources aka Azure services. Within it a storage account was created.
Take some time to get familiar with the portal. You can find more information about it [here](https://docs.microsoft.com/en-us/azure/azure-portal/azure-portal-overview). <br>
    <br>
    <br>

## Your local machine
We are going to set everything up, so you can work on Azure resources from your local machine. There are multiple options to interact with Azure and you can chose yourself how to do it later on.
1. (optional) Install Visual Studio Code from [here](https://code.visualstudio.com/Download) to handle any code you are going to need. You could of course use a different development environment, we just like this one.
1. (optional) Install the Windows Terminal. You can get it [here](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701?activetab=pivot:overviewtab). It is a command-line front-end and can run Command Prompt, PowerShell, WSL2, SSH and an Azure Clound Shell Connector. Again there are other options, but we like this one.
1. Open the shell and download the Azure CLI from [here](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli). You can also install it without manual downloading using the PowerShell (Run as Administrator):
    ```shell
    Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi'; rm .\AzureCLI.msi
    ```
1. You might need to close and reopen your shell at this point. After that you can connect to your Azure subscription from the shell. To do so enter the following command:
    ```shell
    az login
    ```
    A login should pop up. Enter your credentials there. Now that we are locked into Azure we need to connect to the right subscription. It is possible that you have more than one subscription under the identity you logged in with. In the output from the shell you should see a detailed list of these subscriptions. If not the following command will print it out.
    ```shell
    az account list
    ```
    One of this subscriptions should be shown as ```"isDefault": "true"```. This is the one you are currently connected to. If this is not the right subscription switch like this:
    ```shell
    az account set --subscription <NAME OR ID OF YOUR SUBSCRIPTION>
    ``` 
    Now let's see if it works:
    ```shell
    az group list
    ```
    This should show you all the resource groups in your Azure subscription - so at least a resource group named "cloud-shell-storage-westeurope" you created in the previous section.
1. Get Git [here](https://git-scm.com/downloads) - it helps you to track changes in files, specifically code. We are going to need it to clone this repo to our local machine.
1. If you haven't yet, create a [GitHub](https://github.com/join) account. This service for software development and version control is used by over 83 million developers and we will use it for automation later on.
1. Connect to ypur GitHub account from your local machine by entering:
    ```shell
    git config --global user.name "YourUserName"
    ```
    ```shell
    git config --global user.email "YourUserEmail"
    ```
1. You might need to restart your shell at this point. After that clone the current repository to your local machine.
    ```shell
    git clone https://github.com/alschroe/AzureIoTHack.git
    ```
    You can open it up in Visual Studio Code like this - or with the IDE of your choice.
    ```shell
    cd AzureIoTHack
    code .
    ```
1. Download the PuTTY installer from [here](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) and install it.
This should do for now. <br>
    <br>
    <br>

## Your Raspberry Pi
The Raspberry Pi is a single-board computer and needs to be properly setup so we can use it. Therefore we first need to install an OS - specifically the Raspberry Pi OS (not longer called Raspbian). As said in the beginning you are going to need a desktop monitor, a keyboard and a mouse to interact with this little computer. If you do not have these at home - or not the right cables to connect them to your Pi - you can also access it via SSH. We will offer you both options below. <br>
    <br>

### Option 1: WITH desktop, keyboard and mouse connected to the Pi
<details>
  <summary>If you have all the hardware at hand use this option!</summary>

1. If you do not have a preinstalled Raspberry Pi OS on the micro SD card, then follow the next steps. If you have a preinstalled Raspberry Pi OS on the micro SD card, then you can skip the following steps.
    1. Let's start by downloading the Raspberry Pi OS from [here](https://www.raspberrypi.com/software/). When installing it you will be asked to choose the correct Operating System. Click *CHOOSE OS* and select *Raspberry Pi OS (recommended)*
    1. Insert the micro SD card into your local machine. If you have used the SD card before, make sure to format it.
    1. Under *SD Card* click *CHOOSE SD CARD* and make sure you select the right storage space that represents your micro SD card.
    1. After that hit *WRITE*. This will flash the OS to your micro SD card. It might take a moment. After that hit *CONTINUE*
    1. You will need to very shortly remove the micro SD card and insert it again into your local machine. 
1. Now we want to set up our SSH connection. There are other options to this. But ours will be fast, uncomplicated and replicable in real world cases. In the folder [raspberrypi_ssh](../raspberrypi_setup/raspberrypi_ssh) in this repo you will find two files. The *wpa_supplicant.conf* file contains all the information your Pi needs to connect to your home network. Open it and enter your network name and password. Don't forget to save the changes. The other file is called *ssh* - without file extension. This file will automatically enable SSH on your Pi.
1. Then access the boot folder on your micro SD card and paste the two files in them.
1. Eject the SD card securely and insert the micro SD card into your Raspberry Pi.
1. Now first connect your desktop monitor, your keyboard and your mouse to the Raspberry Pi.
1. Connect your Pi to a power resource.
1. You might be prompted with a login.
    The default login is **pi** and the default password is **raspberry**.
1. We want to change that. So once you are on your Raspberry Pi, open the terminal and enter the following.
    ```bash
    sudo raspi-config
    ```
    The Configuration Tool will open up and show you a bunch of options.
    Select *1 Change User Password | Change password for the 'pi' user* by hitting enter while it is highlighted. Make sure to remember your password.
    Select *OK* and after that - in the main overview of the Configuration Tool - select *Finish* to exit the tool by using the tab key on your keyboard.
1. Open a terminal on your Pi. We want to install the Azure CLI here as well to make our lives easier in the long run. Enter this command:
    ```bash
    curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
    ```
1. You potentially need to restart the terminal. After that log in to your Azure subscription:
    ```bash
    az login
    ```
</details> <br>

### Option 2: WITHOUT desktop, keyboard and mouse connected to the Pi | via SSH
<details>
  <summary>If you don't need a UI go via the shell!</summary>

SSH is the Secure Shell Protocol and used to securely connect to another device over an unsecure network.
1. Let's start by downloading the Raspberry Pi OS from [here](https://www.raspberrypi.com/software/). When installing it you will be asked to choose the correct Operating System. Click *CHOOSE OS* and select *Raspberry Pi OS (other)* --> *Raspberry Pi OS Lite*.
1. Insert the micro SD card into your local machine. If you have used the SD card before, make sure to format it.
1. Under *SD Card* click *CHOOSE SD CARD* and make sure you select the right storage space that represents your micro SD card.
1. After that hit *WRITE*. This will flash the OS to your micro SD card. It might take a moment. After that hit *CONTINUE*
1. Now we want to set up our SSH connection. There are other options to this. But ours will be fast, uncomplicated and replicable in real world cases. In the folder [raspberrypi_ssh](../raspberrypi_setup/raspberrypi_ssh) in this repo you will find two files. The *wpa_supplicant.conf* file contains all the information your Pi needs to connect to your home network. Open it and enter your network name and password. Don't forget to save the changes. The other file is called *ssh* - without file extension. This file will automatically enable SSH on your Pi.
1. You will need to very shortly remove the micro SD card and insert it again into your local machine. Then access the boot folder on your micro SD card and paste the two files in them.
1. Eject the SD card securely.
1. Instert the micro SD card into your Raspberry Pi.
1. Connect your Pi to a power resource. Let it stew for a moment - maybe grab a coffee.
1. Open PuTTY - you installed it in the beginning.
    1. Under *Host Name (or IP addess)* enter ```raspberrypi.local```.
    1. Under *Port* ```22``` should already be entered, if not do so.
    1. Lastly select *Open*
    1. You should be prompted for login. The default login is **pi** and the default password is **raspberry**.
1. Now you are able to work on the Pi. The first thing we want to do is changing the default password. Type in:
    ```bash
    sudo raspi-config
    ```
    The Configuration Tool will open up and show you a bunch of options.
    Select *1 Change User Password | Change password for the 'pi' user* by hitting enter while it is highlighted. Make sure to remember your password.
    Select *OK* and after that - in the main overview of the Configuration Tool - select *Finish* to exit the tool by using the tab key on your keyboard.
1. We want to install the Azure CLI here as well to make our lives easier in the long run. Enter this command:
    ```bash
    curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
    ```
1. You potentially need to restart the terminal. After that log in to your Azure subscription:
    ```bash
    az login
    ```
</details> <br>

### Option 3: WITHOUT desktop, keyboard and mouse connected to the Pi | via remote desktop
<details>
  <summary>This will be the easiest and most convenient option!</summary>

SSH is the Secure Shell Protocol and used to securely connect to another device over an unsecure network. VNC stands for Virtual Network Computing and will allow you to view the Desktop of your Pi on your local machine, so you do not need to connect the Pi to a desktop monitor etc.
1. Let's start by downloading the Raspberry Pi OS from [here](https://www.raspberrypi.com/software/). When installing it you will be asked to choose the correct Operating System. Click *CHOOSE OS* and select *Raspberry Pi OS (recommended)*
1. Insert the micro SD card into your local machine. If you have used the SD card before, make sure to format it.
1. Under *SD Card* click *CHOOSE SD CARD* and make sure you select the right storage space that represents your micro SD card.
1. After that hit *WRITE*. This will flash the OS to your micro SD card. This will take a moment. After that hit *CONTINUE*.
1. Now we want to set up our SSH connection, network connection and the remote desktop connection. There are other options to this. But ours will be fast, uncomplicated and replicable in real world cases. In the folder [raspberrypi_ssh](../raspberrypi_setup/raspberrypi_ssh) in this repo you will find three files. The *wpa_supplicant.conf* file contains all the information your Pi needs to connect to your home network. Open it and enter your network name and password. Don't forget to save the changes. The other file is called *SSH* - without file extension. This files will automatically enable SSH on your Pi.
1. You will need to very shortly remove the micro SD card and insert it again into your local machine. Then access the boot folder on your micro SD card and paste the two files in them.
1. Eject the SD card securely.
1. Instert the micro SD card into your Raspberry Pi.
1. Connect your Pi to a power resource. Let it stew for a moment - maybe grab a coffee.
1. Open PuTTY - you installed it in the beginning.
    1. Under *Host Name (or IP addess)* enter ```raspberrypi.local```.
    1. Under *Port* ```22``` should already be entered, if not do so.
    1. Lastly select *Open*
        > At this moment you will get a popup asking if you are sure this is your device. And this is a good question. So it is a Raspberry Pi on your network but if you are sharing the network with another colleague who is currently doing the same Hackathon it could be their Pi. There is just no way to know... Coordinate with your colleagues in this case.
    1. You should be prompted for login. The default login is **pi** and the default password is **raspberry**.
1. Now you are able to work on the Pi via shell. The first thing we want to do is changing the default password. Type in:
    ```bash
    sudo raspi-config
    ```
    The Configuration Tool will open up and show you a bunch of options.
    Select *1 Change User Password* by hitting enter while it is highlighted. Make sure to remember your password.
1. In the same Configuration Tool we now want to set the resolution of your Pi. Navigate to *7 Advanced Options* and hit enter. Than select *A5 Resolution* and there the screen resolution of your choosing. Select *OK*.
1. After this we want to change the Hostname. Navigate to *2 Network Options* and than *N1 Hostname*. Make again sure to remember your hostname. 
1. Last navigate to *3 Interface Options* and from there to *P3 VNC*. Enabling this option will help us set up our remote monitor in the next steps. Back in the main overview of the Configuration Tool - select *Finish* to exit the tool by using the tab key on your keyboard. 
    > Be aware that having SSH and VNC activated opens two ports on your Pi. In a productive scenario this is not ideal. If you insist on remote desktop options in production make yourselfs familiar with SSH X11 Forwarding.
1. If you are being asked to reboot the Pi, select *Yes*. If not type the following back in the shell:
    ```bash
    sudo reboot
    ```
1. Now you need to install one more tool - a VNC Viewer. Download it from [here](https://www.realvnc.com/en/connect/download/viewer/) and install it. We did not do this in the beginning, since not everyone will have chosen the remote desktop option.
1. Enter ```YOUR NEW HOSTNAME``` or the IP address of the Raspberry Pi in the text field. An authentication window should pop up. Enter the *Username* ```pi``` and your previously changed *Passowrd*. Select *OK* and you will have a remote desktop connection to your Pi. Important: You have to be outside of VPN to be able to connect to the Raspberry Pi!
1. Open a terminal on your Pi. We want to install the Azure CLI here as well to make our lives easier in the long run. Enter this command:
    ```bash
    curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
    ```
1. You potentially need to restart the terminal. After that log in to your Azure subscription:
    ```bash
    az login
    ```
</details> <br>
    <br>
    <br>

## Your Sense HAT
Now we have our Pi and our local machine set up, we need to get some sensor data. To do that, we have the **Sense HAT** and it is awsome. First things first - HAT means Hardware on Top and offers you an Expansion Board that is specifically created to be put right on top of your Pi to give you functionalities as lights, motors, sensors and fans. The Sense HAT offers us lights and sensors as well as a joystick. Here the sensors:
- Gyroscope
- Accelerometer
- Magnetometer
- Temperature
- Barometric pressure
- Humidity
You see that you can do a lot with the information collected by the Sense HAT. Let's set it up properly.
1. First take your Pi off power.
1. Screw the standoffs - the screws and the little black funnels into all four corners of your Pi.
1. After this you can plug the Sense HAT into the GPIO (General Purpose Input/Output) pins on the Pi so that the Sense HAT and the Pi align perfectly.
1. Use the remeining screws to fix the Sense HAT to the Pi.
    > Before putting your Pi back on power, be aware that the Sense HAT will activate all the LEDs right away and it will be VERY BRIGHT. This shocked all of us so be prepared ;)
1. Now put your Pi back on power and connect to it. The three different options are listed below. You will need to wait a moment until the Pi is up and running.
    1. WITH desktop: Plug your Pi in again and open the terminal on your Pi
    2. via SSH: Open PuTTY again and log into your Pi.
    3. via VNC: Open the VNC Viewer and log into your Pi with your custom hostname. Open the terminal on your Pi.
1. Once you are working on your Pi update it:
    ```bash
    sudo apt update
    ```
1. For the most important part you need to install the Sense HAT dependencies:
    ```bash
    sudo apt-get install sense-hat
    ``` 
    <br>
    <br>
    <br>

## Try it!
Now you are all set up. If this felt anti climactic - well this is just the preparation for the workshop. But we want you to run a litte test on your Pi+Sense HAT. You will find the Python file [color.py](../raspberrypi_setup/color.py) in this repo. We prepared this code for you to make sure your setup is complete and for you to have a little fun with it. It offers you the option to activate LEDs on the Sense HAT by using the little joystick. There are colors and images pre implemented, but feel free to make it your own and be creative with it! Take a picture and send it to the organizers of this event. 

If you have chosen Option 1 or 3 while setting up your Pi and want to work directly on the Pi...
<details>
  <summary>...click here!</summary>

1. Install Git on your Pi by opening the terminal and typing
    ```bash
    sudo apt install git
    ```
    You might need to close and reopen the terminal after that. Than you can clone this repo again.
    ```bash
    git clone https://github.com/alschroe/AzureIoTHack.git
    ```
1. Navigate to the *color.py* file in it's folder:
    ```bash
    ii AzureIoTHack/raspberrypi_setup
    ```
1. Run the code like this:
    ```bash
    python color.py
    ```
1. Now test if the joystick changes the LEDs on your Sense HAT.
1. If you want to adapt what is being shown, you can always open the file in one of the IDEs you will find in the Pi's menue under *Programming*. Choose *Thonnys Python IDE*. Than you just need to hit the run button.
1. Don't forget to take a picture.

</details> <br>

If you have chosen Option 2 or 3 while setting up your Pi and want to work via SSH connection...
<details>
  <summary>...click here!</summary>

1. You need to get the IP address of your Pi. So connect to it again using PuTTY (*raspberrypi.local*).
    ```bash
    ifconfig
    ```
    This should display th IP address of your Pi behind *wlan0* and than *inet*.
1. Open up a shell on your local machine and navigate to the *color.py* file. If you are already in the AzureIoTHack directory you only need to move to the *raspberrypi_setup* folder:
    ```shell
    cd /raspberrypi_setup
    ```
1. Copy the *color.py* file to your Pi by typing:
    ```shell
    scp color.py pi@YOURPISIPADDRESS:
    ```
    Don't forgeth the *:* in the end.
1. Switching back to the Pi terminal you still have open on your local machine, type
    ```bash
    ls
    ```
    to find your file. You should see it listed.
1. Now you can run it, by simply typing:
    ```bash
    python color.py
    ```
1. Now test if the joystick changes the LEDs on your Sense HAT.
1. If you want to make it your own, open the IDE of your choice on your local machine, save the changes you made to the *color.py* file and repeat the steps above. If you are in the right directory and want to use VS Code:
    ```shell
    code .
    ```
1. Don't forget to take a picture.

</details>

<br>

**We are looking forward to our Hackathon!**
