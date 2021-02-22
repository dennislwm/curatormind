# How to Run Docker on Synology NAS

## Change SSH Password

**Enable SSH**

You can enable SSH service from the **Control Panel**. Go to **Terminal & SNMP** and check the box **Enable Telnet service** and click **Apply**.

After enabling SSH service, you will find out that you are unable to login with the admin password on the Synology web interface. We will need to temporarily enable Telnet service to fix this problem.

**Temporarily Enable Telnet**

Under the previous Terminal & SNMP, check the box **Enable Telnet service** and click **Apply**. You have to connect to both Telnet and SSH on the LAN because it doesn't work with Quickconnect.

On your iPhone, download the app iTerminal. Open the app and create a Telnet connection specifying your IP address on port 23, e.g. 192.168.86.31.

Login using admin and same password as your Synology web interface. Type the following command to change the SSH password:

     $ sudo synouser --setpw admin {password}

If it doesn't work the first time, try again. After changing the password, you should be able to SSH to your Synology.

Return to your Synology **Control Panel**, and disable the Telnet service. 

*Warning*: Changing the admin password using Task Scheduler but did not work.

**Connect via SSH**

Using the app iTerminal, create an SSH connection specifying your IP address on port 22. Login using admin and your password, and type EXACT:

     $ sudo ln -s /var/run/docker.sock /volume1/docker/docker.sock

*Warning*: Even after creating the symlink you cannot create the container from the Docker UI. This is because symlinks are not listed when trying to create a volume/file link.

There are two possible solutions: 

* Create container from the command line via SSH
* [Setup and install Portainer on Synology NAS](https://nashosted.com/setup-and-install-portainer-on-synology-nas) 

## Install Portainer

The Synology Docker UI is nice but lacks some functionality such as Stacks, Templates, etc. Portainer will run seamlessly along side the Synology Docker UI.

First make a folder on your Synology Web Interface to hold the portainer data, using File Station, i.e. /`DBDock/docker/portainer`.

However before we can install **Portainer**, we need to login via SSH as `root` (password is same as your Synology Web Interface).

     $ sudo -i

Now run the following command to grab the Portainer image. *Warning*: Do not change volume1 as it is the EXACT name of DBDock.

     # docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /volume1/docker/portainer:/data portainer/portainer

Now check to see if it worked, you need to access the Portainer container from your LAN, i.e. 192.168.86.31, on port 9000. Create the admin user and password.

Once logged in, select the **Local** environment and press the **Connect** button. You should be able to see a Dashboard of all your Docker files.