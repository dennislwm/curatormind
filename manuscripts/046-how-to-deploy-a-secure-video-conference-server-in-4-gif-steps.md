# How to Deploy a Secure Video Conference Server in 4 GIF Steps

[Jitsi Meet](https://jitsi.org/) is a secured, fully featured, and completely free video conferencing.

Jitsi isn't just for video conferencing. It also has room chat, virtual hand raise, screen sharing and streaming a YouTube video. Using your own Jitsi server, you don't have to be tied down to any free public servers, which may laggy or not available 24/7, or to be dependent on premium servers, which may be expensive. And if you're a serious user, you can create a scalable, optimized video conferencing server, or even a frontend graphical user interface to wrap around your server.

## Introduction

**TL;DR**

In this article, you'll create a new DigitalOcean account using a free credit link. Then, you will clone a GitHub repository, and use Terraform code to initialize, plan and apply resources to your account, using a preconfigured image ffrom DigitalOcean and your custom domain name. Finally, you will access your account remotely via SSH and execute two scripts to configure both your video conference server and Let's Encrypt SSL to enable HTTPS.

## Prerequisites

* Custom domain name. The article [How to Change DNS for a Domain](https://www.digitalocean.com/community/tutorials/how-to-point-to-digitalocean-nameservers-from-common-domain-registrars) walks you through changing DNS for your domain name.

* [DigitalOcean](https://bit.ly/dbaff001) account. The tutorial [How to Create a Personal Access Token](https://www.digitalocean.com/docs/apis-clis/api/create-personal-access-token/) walks you through creating a DigitalOcean access token.

* SSH Keygen. Requires a Windows Subsystem for Linux or macOS.

* [Terraform](https://www.terraform.io/). The installation [Install Terraform](https://learn.hashicorp.com/terraform/getting-started/install.html) walks you through installing and configuring your Terraform program.

## Step 1 - Clone GitHub Repository

**Free Credit**

You can signup a DigitalOcean ["DO"] account with up to [$100 credit here](https://bit.ly/dbaff001), which should last you up to 2 months, as unused credit expires after 60 days.

After using up the free credits in one DO account, you can then re-signup for a another DO account using the same link above. However, these are some limitations:

(1) Your credit card details is required for each DO account (but you will not be charged until the credits are used up).

(2) You will need to redeploy all your dockerized web apps, and reroute all your links to new DO IP addresses.

(3) You will need to use a new email address for each DO account.

You can use an anonymous email forwarding app, such as [AnonAddy.com](http://anonaddy.com), to create unlimited email aliases.

**Clone Repository**

Clone this repository to your folder **myproject**:

     $ git clone https://github.com/dennislwm/terraform-jitsi-ssl myproject

## Step 2 - Generate a SSH Key

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

![][1]

[1]: images/046-how-to-deploy-a-secure-video-conference-server-in-4-gif-steps/step-2---generate-a-ssh-key.gif

## Step 3 - Terraform Initialize, Plan and Apply

**Configuration**

(a) In the **root** folder, edit the *variables.tf* file and change the default values for:

* **strSshPath** - path to local SSH folder, e.g. "c:\\users\\dennislwm\\.ssh\\"

* **strSshId** - name of local SSH public_key *.pub* file, e.g. "id_rsa.pub"

* **strRootPath** - path to this project root folder, e.g. "c:\\users\\dennislwm\\terraform-jitsi-ssl\\"

* **strDoDomain** - name of custom domain, e.g. "example.com"

(b) Create a *terraform.tfvars* file and add the following (replace the "token" with your DigitalOcean API access token string):

**strDoToken** = "token"

Warning: Keep your *terraform.tfvars* file (add to *.gitignore*) a secret to prevent unauthorized access to your DigitalOcean account.

**Execution**

Terraform has commands to deploy resources in a very simple way. This consists of three steps:

* Init

* Plan

* Apply

(1) In the **tf** folder, initialize Terraform nested modules by typing the following command:

     $ terraform init

Note: You must execute the init command after each nested module changes.

(2) Create Terraform plan by typing the following comand:

     $ terraform plan

(3) Execute Terraform plan by typing the following command:

     $ terraform apply

Warning: Before typing 'yes', ensure ALL resources Terraform will create and DESTROY are correct.

![][2]

[2]: images/046-how-to-deploy-a-secure-video-conference-server-in-4-gif-steps/step-3---terraform-initialize--plan-and-apply.gif

## Step 4 - Access Your Droplet Console

This article [How to Change DNS for a Domain](https://www.digitalocean.com/community/tutorials/how-to-point-to-digitalocean-nameservers-from-common-domain-registrars) walks you through changing DNS for your domain name.

Warning: If you do not change the DNS, you will NOT be able to configure Jitsi in the next step.

**Preconfigured Scripts**

The preconfigured image has scripts to configure Jitsi in a very straightforward way. This consists of two steps:

* Configure Jitsi Server

* Configure Let's Encrypt SSL

Login to your DigitalOcean account and access the console for your droplet via SSH.

(1) Configure Jitsi server by typing the following:

     $ ./01_videoconf.sh

The menu will ask you to type a domain name, which you added to your project above. Enter exactly the same, eg. markit.work.

When prompted, select the menu option to "Generate a Self-Signed Certificate", unles you want to import your own certificate.

Warning: If you enter an IP address instead, you will NOT be able to configure HTTPS in the next step.

(2) Configure Let's Encrypt SSL to enable HTTPS by running the following:

     $ ./02_https.sh

The script will ask you for an E-mail, which will be used to notify you when it's time to renew the certificates to maintain your website with a secure connection.

**Congratulations!** You have successfully deployed and configured your secure video conferencing server.

To test your new server, open a web browser and navigate to your domain via HTTPS, e.g. https://markit.work

![][3]

[3]: images/046-how-to-deploy-a-secure-video-conference-server-in-4-gif-steps/step-4---access-your-droplet-console.gif

## Understand Terraform Project and Resources

**Project Structure**

Your project folder should look like this:

          terraform-jitsi-ssl/             <-- Root of your project
            |- package.json                <-- Node.js project entries
            |- README.md                   <-- This README markdown file
            +- bin/                        <-- Holds any executable files
               |- mkswap.sh                <-- Creates a swapfile in a Bash terminal for production
            +- tf/                         <-- Terraform root folder
               |- main.tf                  <-- Main TF file (required)
               |- variables.tf             <-- Default variables declaration file for root
               |- outputs.tf               <-- Default outputs declaration file for root
               |- terraform.tfvars         <-- Secret variables declaration file for tokens (.gitignore)
               +- modules/                 <-- Nested modules
                  +- jitsi/                <-- Holds any TF files for Jitsi
                     |- jitsi.tf           <-- Resources TF file
                     |- variables.tf       <-- Overrides inputs of variables.tf file in root
                     |- outputs.tf         <-- Returns outputs in module to main.tf file in root

**Resources**

Let's take a peek at how Terraform resources work, in particular the *main.tf* and *jitsi.tf* files.

Terraform requires a *main.tf* file, in the **root** folder, that accesses global variables and global return values in *variables.tf* and *outputs.tf* files, respectively.

The primary function of *main.tf* file is to contain all global resources, such as provider **digitalocean** and resource **digitalocean_ssh_key**.

The secondary function of *main.tf* file is to import all modules and their return values, such as module **jitsi**.

For example, this is the source code for importing a module:

     module jitsi {
       //
       //  Use relative path to root folder
       source = "./modules/jitsi/"
       //
       //  Use default variables.tf in root folder
       //  Override variables.tf file in modules folder (exclude from main)
       //    strDoProject
       //    strDoImage
       //    strDoSize  
       //
       objSshKey   = [digitalocean_ssh_key.objSshKey.fingerprint]
       strSshPath  = var.strSshPath
       strSshPte   = var.strSshPte
       strRootPath = var.strRootPath
       strDoRegion = var.strDoRegion
     }

**Modules**

Each module contains resources for a droplet, e.g. **jitsi**, which has a separate folder, under the *modules* folder.

Terraform requires a resource file, such as *jitsi.tf*, in the **modules** folder, that access module variables and module return values in *variables.tf* and *outputs.tf* files, respectively.

The **modules** folder structure, which consists of THREE (3) TF files, is similar to the **root** folder structure. However, the **modules** declaration override any global declaration in the **root** folder.

The primary function of *jitsi.tf* is to contain all local resources, such as resource **digitalocean_droplet**, resource **digitalocean_domain**, etc. It also inherits any global resources from *main.tf*, such as provider **digitalocean**.

The secondary function of *jitsi.tf* is to contain any provisions, which are non-state objects, such as provisioner **remote-exec**, **file**, etc. These are specific tasks that depends on the resources deployed.

**Variables**

Terraform loads variables in the following order, with later sources taking precedence over earlier ones:

  1. Environment variables

  2. The terraform.tfvars or terraform.tfvars.json files, if present.

  3. Any *.auto.tfvars or *.auto.tfvars.json files, processed in lexical order of their filenames.

  4. Any -var and -var-file options on the command line, in the order they are provided. (This includes variables set by a Terraform Cloud workspace.)

  5. There is no mention of *.tf files, this is because variables declared in *.tf files are concatenated into a single entity before being processed by terraform. Hence this declaration have highest precedence.

Any variables or return values declared within the local module files will override the global variables declared in the main folder. 

As an analogy to writing code, this is similar to a function's local variables overriding the global variables declared in its parent function.

However, unlike code, the local variables.tf file must declare all the global variables even if there is no overriden values. For example:

In the **root** folder, the *variables.tf* file contains:

     variable strDoRegion {
       default     = "sgp1"
       description = "Region for droplet (override in modules)"
     }
     variable strDoSize {
       default     = "s-1vcpu-1gb"
       description = "Size for droplet (override in modules)"
     }

The global variables **strDoRegion** and **strDoSize** contain the default values "sgp1" and "s-1vcpu-1gb", respectively.

In the **modules** folder, the *variables tf* file contains:

     variable strDoRegion {
       description = "Region for droplet (use default in root)"
     }
     variable strDoSize {
       default     = "c-4"
       description = "Size for droplet (override in modules)"
     }

The local variable **strDoRegion** is declared, but its default value is not set (inherits from global), while the local variable **strDoSize** is overridden by setting default to "c-4".

In practice, it is recommended to set ALL default values in the main *variables.tf* file, even if you intend to override these values in the modules *variables.tf* file.

**Outputs**

The modules *outputs.tf* file consists of return values from a remote server, e.g. an IP address.

Any local return values can be accessed from both the *jitsi.tf* and the *main.tf* files. For example:

In the **modules** folder, the *outputs.tf* file contains:

     output "server_ip" {
       value = digitalocean_droplet.objJitsi.ipv4_address
     }

The local return value **server_ip** contains the ipv4_address from the module **jitsi** resource.

In order to access the local return value above from the *main.tf* file, you must declare it in the main *outputs.tf* file.

In the **root** folder, the *outputs.tf* file contains:

     output "server_ip_jitsi" {
       value = module.jitsi.server_ip
     }

The global return value **server_ip_jitsi** is declared, and the value is set to the local return value **server_ip**.

## Conclusion

In this article, you have succesfully achieved:

* clone a GitHub repository

* generate a SSH key

* execute Terraform Init, Plan and Apply to deploy resources

* access your droplet console and execute scripts via SSH

## Get the Source Code

You can download the above source code from GitHub repository [terraform-jitsi-ssl](https://github.com/dennislwm/terraform-jitsi-ssl).

## What To Do Next

You can further explore Jitsi in several meaningful ways:

* Read [Jitsi Documentation](https://github.com/jitsi/jitsi-meet/blob/master/doc/README.md) - This helps you to have a deeper knowledge of Jitsi server.

* Read [Jitsi Projects](https://jitsi.org/projects/) - This helps you explore a collection of projects within Jitsi ecosystem.

* Read [Jitsi Blog](https://jitsi.org/blog/) - This helps you find relevant information about Jitsi features.

* Participate in [Jitsi Community](https://community.jitsi.org/) - This helps you to have a meaningful discussion online with other users of Jitsi.

* Read [Terraform Documentation](https://www.terraform.io/docs/index.html)