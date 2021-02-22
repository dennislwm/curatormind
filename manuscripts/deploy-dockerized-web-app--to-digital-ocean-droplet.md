# Deploy Dockerized Web App  to Digital Ocean Droplet

## Step 1 - Sign Up for a DigitalOcean Account

You can signup a DigitalOcean ["DO"] account with up to $100 credit [here](https://try.digitalocean.com/performance/), or [here](https://try.digitalocean.com/kubernetes-in-minutes/), which should last you up to 2 months, as unused credit expires after 60 days.

You should choose a DO droplet with the cheapest tier of $5 per month.

After using up the free credits in one DO account, you can then re-signup for a another DO account using the same link above. However, these are some limitations:

(1) Your credit card details is required for each DO account (but you will not be charged until the credits are used up).

(2) You will need to redeploy all your dockerized web apps, and reroute all your links to new DO IP addresses.

(3) You will need to use a new email address for each DO account.

You can use an anonymous email forwarding app, such as [AnonAddy.com](http://anonaddy.com), to create unlimited email aliases.

## Step 2 - Create a Droplet in a GIF

The goal is to create the cheapest plan for your droplet. 

(1) Choose an image: Ubuntu

(2) Choose a plan: Standard ($5/month)

(3) Choose a datacenter region: New York

(4) Select additional options (ensure the following are checked):

* Private networking

* IPv6

* Monitoring

(5) Authentication: One-time password

(6) Optionally, add tags: Teedy

(7) Click on **Create Droplet **button.

The details of your new droplet will be emailed to you, including your IP address and root password.

![][1]

[1]: images/deploy-dockerized-web-app--to-digital-ocean-droplet/step-2---create-a-droplet-in-a-gif.gif

## Step 3 - Generate a SSH Key

You can create an SSH key with either OpenSSH, which is included in on Windows Subsystem for Linux ["WSL"] and macOS.

(1) Open a Command Prompt or Terminal window and enter the following command:

     > ssh-keygen

(2) Enter file in which to save the key. For example, c:\users\denbrige\.ssh\id_rsa_do1

When prompted to enter a paraphrase, just press the Enter key for an empty paraphrase.

(3) Open the config file using Notepad:

     > notepad c:\users\denbrige\.ssh\config

(4) Append the following line in the file:

     IdentityFile c:\users\denbrige\.ssh\id_rsa_do1

The SSH checks each ssh key within the *config* file when attempting to establish an SSH connection.

![][2]

[2]: images/deploy-dockerized-web-app--to-digital-ocean-droplet/step-3---generate-a-ssh-key.gif

## Step 4 - Access Console and Change Root Password

(1) Check your email for the details of your new droplet. For example:

* Droplet Name: ubuntu-s-1vcpu-1gb-nyc1-01

* IP Address: 142.93.12.220

* Username: root

* Password: *****

(2) Login to your DigitalOcean account and access the console for your droplet.

Change your root password as you will be prompted the first time you login.

## Step 5 - Upload SSH Public Key to Existing Droplet

(1) Open a Bash Terminal window and enter the following command:

     $ ssh-copy-id -f -i c:\\users\\denbrige\\.ssh\\id_rsa_do1 root@142.93.12.220

* -f force mode

* -i use the given IdentityFile

Note: replace the above ip address with your droplet's ip address

(2) Test your SSH connection by typing the following command:

     $ ssh root@142.93.12.220

![][3]

[3]: images/deploy-dockerized-web-app--to-digital-ocean-droplet/step-5---upload-ssh-public-key-to-existing-droplet.gif

## Step 6 - Install Docker and Pull Image

(1) Open a Command Prompt window and login to your account via SSH connection:

     > ssh -F c:\\users\\denbrige\\.ssh\\config root@142.93.12.220

* -F full path of your config file

(2) Type the following command to update existing packages:

     # apt update

(3) Install docker (including docker-compose) by typing the following command:

     # snap install docker

![][4]

[4]: images/deploy-dockerized-web-app--to-digital-ocean-droplet/step-6---install-docker-and-pull-image.gif

## Step 7 - Create a Swapfile on Existing Droplet

## Step 8 - Upload Files to Existing Droplet

## Step 9 - Run Container

- Expose Container port to 80

- Mount Container folder to a new folder