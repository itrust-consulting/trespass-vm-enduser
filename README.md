# Information
## OS
OS type: Ubuntu Server 16.04.1

Username: `trespass` 

Password: `trespass`

## SSH
A SSH server is running on the virtual machine so it eases the administration (See `Further Information` section for tutorial on using SSH)

## IP address
The IP of the machine is displayed on the console of the virtual machine. If no IP has been set (e.g. missing interface, no dhcp) a message is displayed, warning to check if an interface is set to the machine, or if the interface is on a network with a DHCP server.

## Attack navigator map
The attack navigator map application requires to do a refresh (F5 key) of the page (/attack-navigator-map/index.html) on the first access as a bug prevent loading of model

# Steps to start and access the trespass services
1. Download ova [file](https://www.itrust.lu/trespass-enduser.ova) and save it on your disk;
1. Import the ova to your virtual machine system;
1. Check that the interface of the virtual machine is set to a host interface (bridge mode) connected to a network where a DHCP server is running;
1. Start the machine;
1. Wait for the logon console (The IP address of the machine should appears before the logon);
1. On your desktop or laptop machine, if under Windows, open notepad as administrator, then open following file: `c:\Windows\System32\Drivers\etc\hosts`; if under Linux, open following file as administrator: `/etc/hosts`; if under MacOS, open following file as administrator: `/private/etc/hosts`.
1. append the file with following lines (replacing IP_ADDRESS with the IP address from step 5)
```
IP_ADDRESS	trespass.local  
IP_ADDRESS	cas.trespass.local  
IP_ADDRESS	svn.trespass.local  
IP_ADDRESS	arguesecure.trespass.local  
IP_ADDRESS      interactor.trespass.local  
IP_ADDRESS      redmine.trespass.local  
IP_ADDRESS      tkblogs.trespass.local  
```

1. You can access the front-end using your browser with the following address [https://trespass.local](https://trespass.local). A default user (with administrator role) has been defined with following credentials:  
Username: `trespass`  
Password: `Tresp@ss1`
1. Click on `Edit` link in `Manage account` section:  
![Edit profile](./home.png  "Edit profile")
1. Click on `Edit contact` button:  
![Edit profile](./editcontact.png  "Edit contact")
1. Edit `Email address` input with your email (so you will receive email on user registration), and click on `Save` button:  
![Edit profile](./editemail.png  "Edit email")


# Configure reCAPTCHA and email notification
1. Log-on with SSH or directly to the machine console using trespass as user and password (pay attention to the keyboard which is set to swiss french, you can change it as explained in `Change Keyboard mapping` section). 
1. Switch to root with `sudo su` (enter same session password)
1. Change directory to trespass-docker folder with `cd trespass-docker`
1. Edit `fe.env` file with `nano fe.env`, and modify following environment variables for email notification and reCAPTCHA:  
```
RECAPTCHA_SECRET_KEY= \\CAN BE LEFT EMPTY, IN THIS CASE RECAPTCHA IS DISABLED ON REGISTRATION. 
                      \\ACTIVATING RECAPTCHA (i.e. PUTTING A KEY) REQUIRES TO CHANGE
                      \\THE DNS NAME OF FRONT-END. 
RECAPTCHA_PUBLIC_KEY=  
SMTP_HOST=  
SMTP_PORT=  
SMTP_USERNAME=  
SMTP_PASSWORD=  
SMTP_AUTH_ENABLED= \\PUT true IF SMTP SERVER MUST CHECK AUTH, false OTHERWISE  
SMTP_STARTTLS_ENABLED= \\PUT true IF SMTP USES STARTTLS, false OTHERWISE  
```
1. Save your modification with `CTRL+O` and `Enter`
1. Quit nano with `CTRL+X`
1. Restart services of the platform with following command: `/etc/init.d/startContainers`
1. Wait for the end of the command
1. You can acces services

# Configure DNS
1. Log-on with SSH or directly to the machine console using trespass as user and password (pay attention to the keyboard which is set to swiss french, you can change it as explained in `Change Keyboard mapping` section). 
1. Switch to root with `sudo su`  (enter same session password)
1. Change directory to trespass-docker folder with `cd trespass-docker`
1. Edit genvar file with `nano genvar`
1. Put the root DNS you want after equal sign in `NEW_DNS_VALUE=`  line (e.g. trespass.eu)
1. Save with `CTRL+O` and `Enter`
1. Quit nano with `CTRL+X`
1. Restart services of the platform with following command: `/etc/init.d/startContainers`
1. Wait for the end of the command
1. On your desktop or laptop machine, if under Windows, open notepad as administrator, then open following file: `c:\Windows\System32\Drivers\etc\hosts`; if under Linux, open following file as administrator: `/etc/hosts`; if under MacOS, open following file as administrator: `/private/etc/hosts`.
1. append the file with or edit following lines (replacing `IP_ADDRESS` with the IP address of the virtual machine), and DNS_VALUE with the DNS you are setting  
```
IP_ADDRESS	DNS_VALUE  
IP_ADDRESS	cas.DNS_VALUE  
IP_ADDRESS	svn.DNS_VALUE  
IP_ADDRESS	arguesecure.DNS_VALUE  
IP_ADDRESS      interactor.DNS_VALUE  
IP_ADDRESS      redmine.DNS_VALUE  
IP_ADDRESS      tkblogs.DNS_VALUE  
```
1. You can access the front-end using your browser with the new DNS you set up.

# Custom TLS
We recommend you to use recognized CA as current configuration will give you warning message (See next image) from browser (you will need to add Security Exception)
![TLS warning](./TLSException.png  "TLS warning")

Your certificate and keys must be in pem format. In order to put them in the virtual machine, we recommend you to install [Filezilla](https://filezilla-project.org/download.php?type=client).
1. Using tutorial from [Link](https://www.one.com/en/support/faq/how-do-i-connect-to-an-sftp-server-with-filezilla), open Filezilla and connect to the trespass virtual machine
1. When connected, on the left side showing content of your local computer, select the folder where your key and certificate are stored;
1. Send the key and certificate to the trespass virtual machine by double clicking on them.
1. Close Filezilla.
1. Log-on with SSH or directly to the machine console using trespass as user and password (pay attention to the keyboard which is set to swiss french, you can change it as explained in `Change Keyboard mapping` section). 
1. Switch to root with `sudo su` (enter same session password)
1. Change directory to trespass-docker folder with `cd trespass-docker`
1. Move your key with `mv ../KEY_FILENAME data/rp/keys/cert.key` (replacing `KEY_FILENAME` with the filename of your key)
1. Move your certificate with `mv ../CERT_FILENAME data/rp/certs/cert.crt` (replacing `CERT_FILENAME` with the filename of your certificate)
1. Edit genvar with `nano genvar`
1. Replace `CUSTOM_TLS=false` with `CUSTOM_TLS=true`, so your key and certificate are not deleted when restarting services.
1. Save with `CTRL+O` and `Enter`
1. Quit nano with `CTRL+X`
1. Restart services of the platform with following command: `/etc/init.d/startContainers`

# Further Information
## Change Keyboard mapping
The current keyboard mapping of the virtual machine is currently set to: `swiss french`

In order to change the mapping, please use following command:
```
sudo dpkg-reconfigure keyboard-configuration
```

* [Download VirtualBox](https://www.virtualbox.org/)

* [Installing VirtualBox](https://www.virtualbox.org/manual/ch01.html#intro-installing)

* [How to import in VirtualBox](https://www.virtualbox.org/manual/ch01.html#ovf)

* [Introduction to VirtualBox Bridged Networking mode](https://www.linuxbabe.com/virtualbox/a-pretty-good-introduction-to-virtualbox-bridged-networking-mode)

* [Introduction to reCAPTCHA](https://www.google.com/recaptcha/intro/)

* [Using SSSH in Putty](https://mediatemple.net/community/products/dv/204404604/using-ssh-in-putty-)

* [Connecting an SFTP Server with FileZilla](https://www.one.com/en/support/faq/how-do-i-connect-to-an-sftp-server-with-filezilla)
