## Production ready state for Terraform

### TL;DR

We combined `docker-compose.yml` files of both **Nginx Proxy Manager** ["NginxPM"] and **Portainer** into one `docker-compose.yml` file under `docker/nginxpmlite`.

### Production ready state for Docker Compose

1. Create a file `docker/nginxpmlite/docker-compose.yml` and open it in your editor. Copy and paste the following code.

```yaml
version: "3"
services:
  nginxpm:
    image: 'jlesage/nginx-proxy-manager'
    restart: always
    ports:
      # Public HTTP Port:
      - '80:8080'
      # Public HTTPS Port:
      - '443:4443'
      # Admin Web Port:
      - '81:8181'
      # Default Administrator User
      #   URL: http://localhost:81
      #   Email: admin@example.com
      #   Password: changeme
    environment:
      # DB_SQLITE_FILE: "/data/database.sqlite"
      # Uncomment this if IPv6 is not enabled on your host
      DISABLE_IPV6: 1
    volumes:
      - vol_config:/config:rw
    depends_on:
      - portainer
    networks:
      - net_public

  portainer:
    image: portainer/portainer
    command: -H unix:///var/run/docker.sock
    restart: always
    ports:
      - 9000:9000
      - 8000:8000
      # Default Administrator User
      #   URL: http://localhost:9000
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - vol_data_portainer:/data:rw
    networks:
      - net_public
      
volumes: 
  vol_config:
  vol_data_portainer:
  # Named volumes are stored in a part of the host filesystem 
  # which is managed by Docker (/var/lib/docker/volumes/ on Linux)
  # Docker appends [FOLDER] name to named volumes.
  #   nginxpm_vol_config

networks:
  net_public:
  # Docker appends [FOLDER] name to named networks.
  #   nginxpm_net_public
```

2. Open the file `tf/dev/main.tf`.

* insert a new provisioner `remote-exec` to `sudo` update Ubuntu.
* change path to `/root/docker/nginxpmlite/` for an existing provisioner `remote-exec` to stand up a **NginxPM** Docker container.
* remove an existing provisioner `remote-exec` to stand up a **Portainer** Docker container.

```tf
  // ... previous provisioner file

  //
  // update 
  provisioner "remote-exec" {
    inline     = ["sudo apt update -y"]
    on_failure = continue
  }

  // ... previous provisioner remote-exec to get docker

  //
  // docker container nginxpm
  provisioner "remote-exec" {
    inline     = ["sudo /root/bin/docker-compose -f /root/docker/nginxpmlite/docker-compose.yml up -d"]
    on_failure = continue
  }
  //
  // docker container portainer
  // provisioner "remote-exec" {
    // inline     = ["sudo /root/bin/docker-compose -f /root/docker/portainer/docker-compose.yml up -d"]
    // on_failure = continue
  // }
```

## Configuration of Docker Compose

Docker Compose creates the network `nginxpmlite_net_public`, which each container must join to be accessible from outside your server via NginxPM reverse proxy.

Docker Compose creates volumes under `/var/lib/docker/volumes/`for each container. Initially, it creates two volumes for NginxPM and Portainer respectively.
1. nginxpm_vol_config
2. nginxpm_vol_data_portainer

*Note: Portainer creates docker volumes in the same folder above, hence we will require a strategy to backup these volumes to an external storage.*

### Portainer

For Portainer container, Docker Compose exposes and maps both ports `8000:8000` and `9000:9000`, where `[HOST_PORT]:[CONTAINER_PORT]`. However only port `9000` requires a proxy domain.

Portainer admin panel is accessible at `[SERVER_IP]:9000`. It will prompt you to change the admin password on first connect.

### Nginx Proxy Manager

For NginxPM container, Docker Compose exposes and maps three ports `80:8080`, `443:4443` and `81:8181`. Both ports `80`, `443` require ingress TCP security rules, while port `81`requires a proxy domain.

The HTTP and HTTPS traffic uses ports `80` and `443` respectively. The NginxPM admin panel is accessible at port `[SERVER_IP]:81`.

The default email and password for first time login is `admin@example.com` and `changeme` respectively. You will be required to change these.

## Securing traffic using SSL

The SSL protocol requires a registered domain name, such as `example.com`. After you have purchased a domain, you need to signup for a **Cloudflare** account.

### Managing DNS

**Cloudflare** allows you to manage your domain easily and provides proxied DNS, which basically masks your server IP address.

First change the nameservers of your domain to point to **Cloudflare's** nameservers, e.g. `lewis.ns.cloudflare.com` and `nola.ns.cloudflare.com`.  Changes will take effect after about 24 hours.

Go to the dashboard, click "Add a Site" and enter your [DOMAIN_NAME] that you purchased. Then click on "DNS Management" icon on the menu.

Click "+Add Record" and add the following records:
| Type | Name | Content | TTL | Proxied Status |
|------|------|---------|-----|----------------|
| A | @ | [SERVER_IP] | Auto | Proxied |
| A | nginxpm | [SERVER_IP] | Auto | Proxied |
| A | portainer | [SERVER_IP] | Auto | Proxied |

You have associated `[DOMAIN_NAME]`, `nginxpm.[DOMAIN_NAME]` and `portainer.[DOMAIN_NAME]` with your [SERVER_IP].

### Adding Ingress TCP Security Rules

By default, some IaaS automatically allows inbound traffic at both ports `80` and `443`. However, for major cloud providers such as Azure, we need to create an ingress TCP security rule for each port.

### Adding Proxy Host

Since all your domains are associated with your [SERVER_IP], traffic from all domains will be inbound at port `80` of your server.

We use **NginxPM** to force all traffic to direct to port `443` which is secured with SSL. It will also redirect traffic to each individual containers by the rules that we set.

Go to **NginxPM** dashboard at `http:[SERVER_IP]:81`, then click on `Hosts -> Proxy Hosts`.

Click on "Add Proxy Host". Under "Details" tab, enter the following:

* For "Domain Names", we add `nginx.[DOMAIN_NAME]` which is the Cloudflare subdomain that we created.
* For "Scheme", select `https` as we want all traffic to be secured with SSL.
* For "Forward Host Name", type `nginxpmlite_nginxpm_1`, which is the Docker [CONTAINER_NAME] for NginxPM
* For "Forward Port", type `81`, which is the Docker container [HOST_PORT].

Under "SSL" tab, enter the following:

* For "SSL Certificate", click on "Request a New SSL Certificate".
* Enable "Force SSL".

Under "Advanced" tab, copy and paste the following:

```nginx
location / {
    proxy_pass http://[CONTAINER_NAME]:[CONTAINER_PORT];
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
}
```

Click "Save". You'll need to substitute in your values for [CONTAINER_NAME] and [CONTAINER_PORT].

*Note: Most containers will have the same [HOST_PORT] and [CONTAINER_PORT]. However, sometimes they're different and this matters when you're adding a Proxy Host.*

To test your proxy host, open a web browser and navigate to `https://nginx.[DOMAIN_NAME]`. You should see the NginxPM login page secured with SSL.

> Repeat the above steps to add a Proxy Host for Portainer. You will need to change `nginxpm.[DOMAIN_NAME]` to `portainer.[DOMAIN_NAME]`, and use Portainer's values for [CONTAINER_NAME], [HOST_PORT] and [CONTAINER_PORT] instead.
