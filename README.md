# rhel_study_guide
Study Guide for those planning to sit EX300 on RHEL 7

## Initial Steps 
## INSTALL RH7
-- Download RH7 iso from RH website, put on your laptop in an accessible directory

#############
## 1 Download (from virtualbox.org) & Install  Virtualbox
##############
-- Install Oracle VM Virtualbox package


##############
## 2 Configure Virtualbox to use your ISO
##############
- Somewhere along here you need to get Virtualbox to use your ISO
Devices -> CD/DVD Devices -> Choose a CD/DVD disk file
Navigate to find your iso & choose.

- To check
File-> Virtual Media Manager -> Optical Disks
Your ISO should be in the list here



##############
 ### 3 Create VM in Oracle VM VirtualBox Manager
##############


-- Create RedHat Machine
Click on ‘New’ 
Open & choose New Icon   
—-  Name: <VM_name
  Type: Linux
  Version: RedHat (64 bit)
-- Memory: 512M is fine
-- Choose create a virtual Hard Drive now : Min 4G
   File Type : VHD  (or VDI but VHD allows you to manage from OS using OS tools) 
-- Dynamically allocated is OK as we dont need speed. 
   Size:  8 Gig is plenty. 
-- Attach RHEL7 iso image to cdrom drive under storage

##############
 ### 4 Start your new Redhat VM 
##############

Click on VM name & click ‘Start’
At any point you can delete you image by right mouse clicking it, shutting it down & clicking on ‘remove’ in the same menu

Click on the iso image *you will build your redhat VM from this image
State should change in the VM manager window to ‘running’

On boot screen, scroll up to “Install Red Hat Enterprise Linux 7.1”

##############
 ### 5 Install your new Redhat VM 
##############

During OS install you will need to use your ‘Cmd’ key to flick from laptop control to VM control.

Configure the following 

-- If your keyboard has £# on the number ‘3’ you have a UK keyboard, 
   chose English -> United Kingdom
— When prompted during storage setup
  allow the system to setup your partitions
- Accept the changes

- You can use rhel tools later to assign network & hostname & set the date/time
  This will be good practice for you.

- Click ‘Begin Installation’

- Do Set a root pw when prompted. Click on 'Done' twice if it complains that your pw is weak.
  You do not need to create personal user etc at this point
  (its not necessary as you can just use root)
  
- Click 'Reboot' when prompted




##################
## Configure your VM at OS level
##################
-- login as root

-- Set graphical-target to allow command line GUI tools (very useful in RH7 and quicker for exam)
 % systemctl get-default		(if multi-user target .change it to..)
 % system set-default graphical.target
 % systemctl get-default
		  

-- Set hostname using #hostnamectl or #nmtiu 
  % hostnamectl set-hostname system1.network0.example.com

-- Set enforcing mode  
  % vi /etc/selinux/config
  SELINUX=enforcing
  
-- Activate all changes
  % reboot

###############################
### Config yum & CDROM to mount
##################

-- Mount your ISO to be used to install other packages NOT with the base install
Note: the ISO needs to be available through VirtualBox (Devices -> CD/DVD devices etc)
% mkdir -p /mnt/media
% mount /dev/cdrom /mnt/media
% df -h (to check, will be RO only)
You can also add this to your /etc/fstab if desired

-- Configure yum repo to talk to your ISO for yum updates
% cd /etc/yum.repos.d
Note: Files in here MUST have .repo extension
NO other files can exist here unless they have a .repo extn(bug yum will NOT work)
% vi default.repo, paste in the below & save
[default]
Name = Default Repo
baseurl=file:///mnt/media
enabled=1
gpgcheck=0

To check
% yum clean all
% yum repolist


###############################
### Look at the default VM settings
##################

--- What is my ip ?
% ip a
Prob have an IP of nature 10.0.2.5
this is the inbuild DHCP giving your VM an IP.
We'll set our own IP's shortly



-- Not Completely necessary -> Register your host on RHN
Configure Network on your VM to talk to INTERNET TEMPORARILY
Currently you have no outbound connectivity to the Internet
- First Bring down your host 
Click on the VM network icon OR Devices->Network
Network Settings -> Adapter 1-> NAT -> OK
Start the VM
# ping 8.8.8.8
% subscription-manager register
username:<username>@salesforce.com
pw: rhnetwork password





###############################
## ENABLE VM's & your Mac OS to talk 
### 1.   Configure Host-only Network & DHCP
###############################

Within VirtualBox -> Preferences 
In VM Manager -> 
Set up a Host-only network. This requires the following steps:
a) VirtualBox -> Preferences -> Network -> Host-only networks.
 Add one by clicking on the Plus
 sign or if one exists already use that.
 Hit the screwdriver to edit -> DHCP server. 
Check 
Enable DHCP sever
 fill in the mask as you like. 
Reasonable values are: 
Server Address 192.168.56.254
 Server mask 255.255.255.0 \
Lower Address Bound 192.168.56.100 
Upper Address Bound 192.168.56.200

 Now configure your VMs to have at least one adapter on the Host-only network.
 Three are needed if you want to complete theh interface 'teaming' question which is a requirement

###2. 
In each VM -> Set it up to use THREE 'Host Only Network' connections

Machine-> Settings -> Network ->  Adapter 1 ->  Attach to "Host-only Adapter" -> vboxnet0 
Promiscuous Mode: -> ALlow all 
OK

Now your (mac) host has IP 192.168.56.1, and the various VM IP's can be 192.168.56.100 to 192.168.56.200,...

You may need to tell your VM to use a default GW:
  sudo ip route add default gw 192.168.56.1


--- More Notes: virtual host to guest and guest to guest access
https://emmanuelbernard.com/blog/2012/02/28/configuring-virtualbox-guests-to-access-the-internet-and-be-accessible-from-the-host
I believe you can setup an additional Adaptor as NAT so you can permanently talk to the internet over an interface.

############################################################################################################################




