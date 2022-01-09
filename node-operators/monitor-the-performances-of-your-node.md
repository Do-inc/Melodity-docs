---
description: >-
  Every node operator must always be aware of the state of the node he owns,
  this will help you find out any problem asap.
---

# Monitor the performances of your node

## Server requirements

In order to successfully run a validator node, the server should have at least:&#x20;

* CPU: 6+ vCore
* Ram: 16+ Gb
* Storage: 160+ Gb (possibly SSD)
* Bandwith: 1+ Gbps (unmetered)

We suggest you run a [VPS on OVH](https://www.ovh.it/order/vps/?v=3#/vps/build?selection=\~\(range\~%27Elite\~pricingMode\~%27degressivity12\~flavor\~%27vps-elite-8-16-160\~os\~%27ubuntu\_20\_04\~datacenters\~\(SBG\~1\)\)) as the price is pretty low.

## Monitoring your node

In order to monitor your node performance you have to use a couple of tools:

* Prometheus and Prometheus Node Exporter: These will respectively export the blockchain validation status (the last block finalized, number of blocks created etc.) and export the server stats
* Grafana: This will display in an easy to understand form all the data retrieved using Prometheus

{% hint style="info" %}
This tutorial uses Prometheus Node Exporter _v1.3.1_ and Prometheus _v2.32.1_
{% endhint %}

Once connected to your node create two folders, one named _prometheus_ and the other called _prometheus-node-exporter_.

```
mkdir prometheus
mkdir prometheus-node-exporter
```

Then proceed to access the _prometheus_ folder and download the compiled archive for Prometheus from the [official download page](https://prometheus.io/download/).

### Installing and configuring Prometheus

The following commands download Prometheus v2.32.1, unpack the archive and move the files from the default directory to the previously created one.

```
cd prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.32.1/prometheus-2.32.1.linux-amd64.tar.gz 
tar xvf prometheus-2.32.1.linux-amd64.tar.gz
rm prometheus-2.32.1.linux-amd64.tar.gz
mv prometheus-2.32.1.linux-amd64/* ./
rmdir prometheus-2.32.1.linux-amd64
```

Once installed Prometheus using the previous commands edit the configuration file called _prometheus.yml_ running the following command

{% hint style="warning" %}
This command will replace the default _prometheus.yml_ file
{% endhint %}

```
cat > prometheus.yml <<EOF
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label 'job=<job_name>' to any timeseries scraped from this config.
  - job_name: "Melodity Beats node"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    scrape_interval: 5s

    static_configs:
      - targets: ["localhost:9615"]
  - job_name: "node"
    scrape_interval: 15s
    static_configs:
      - targets: ["localhost:9100"]
EOF
```

Now your Prometheus installation is ready to go but not yet active, first we have to set up the Prometheus Node Exporter

### Installing and configuring Prometheus Node Exporter

The setup procedure for the Prometheus Node Exporter is similar to the one of Prometheus, even if it is simpler.

The following commands download Prometheus Node Exporter v1.3.1, unpack the archive and move the files from the default directory to the previously created one.

{% hint style="warning" %}
If you're running this script after the previous section you have to move back in the directory tree using `cd ..`
{% endhint %}

```
cd prometheus-node-exporter
wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz
tar xvf node_exporter-1.3.1.linux-amd64.tar.gz
rm node_exporter-1.3.1.linux-amd64.tar.gz
mv node_exporter-1.3.1.linux-amd64/* ./
rmdir node_exporter-1.3.1.linux-amd64
```

The installation of the node exporter has concluded now it's time to run them

### Running Prometheus and Node Exporter under Supervisor

As a validator, everything you set up must survive (or at least restart) to every error, this is achieved through the usage of _supervisor_&#x20;

{% hint style="info" %}
If you followed the [Validators](setting-up-a-node.md#setting-up-a-validator-node) tutorial you already installed supervisor, if you have not please install it before continuing.
{% endhint %}

Before continuing you must escalate to _root_.\
Choose your preferred way to do it such as `sudo -s` or `su -` or any other method.

First of all, let's write the two configuration file for Supervisor.

```
cat > /etc/supervisor/conf.d/prometheus.conf <<EOF
[program:prometheus]
autorestart=true
autostart=true

command=/home/<username>/prometheus/prometheus --config.file prometheus.yml
directory=/home/<username>/prometheus/

user=<username>

stdout_logfile=/home/<username>/prometheus/prometheus.log
stdout_logfile_maxbytes=10MB
stderr_logfile=/home/<username>/prometheus/prometheus.error.log
stderr_logfile_maxbytes=10MB
EOF
cat > /etc/supervisor/conf.d/prometheus_node_exporter.conf <<EOF
[program:prometheus_node_exporter]
autorestart=true
autostart=true

command=/home/<username>/prometheus-node-exporter/node_exporter

user=<username>

stdout_logfile=/home/<username>/prometheus-node-exporter/node_exporter.log
stdout_logfile_maxbytes=10MB
stderr_logfile=/home/<username>/prometheus-node-exporter/node_exporter.error.log
stderr_logfile_maxbytes=10MB
```

The two configuration files make the node exporter and the Prometheus executables start automatically. Remember to change _\<username>_ with your username.

Now that the configuration files are correctly set up its time to start them, simply run

```
supervisorctl reread
supervisorctl update
supervisorctl status
```

You'll see an output stating that the two programs just added are running. Prometheus is now correctly set up, time to move to Grafana.

### Installing and configuring Grafana

Grafana may be installed as a standalone program or using one of the repositories provided by the team. We strongly suggest the second method and in this tutorial we'll use that one.

Follow the installation instruction on the [official Grafana page](https://grafana.com/docs/grafana/latest/installation/debian/#install-from-apt-repository) to install it through the official and always up to date channels.

Once you have successfully installed Grafana you'll find yourself having the following file _/etc/grafana/grafana.ini_ in order to set up your instance in a secure way edit that file and modify it as suggested in the following code snippet

```
# ...
app_mode = production


# ...
[server]
# Protocol (http, https, h2, socket)
protocol = http

# The ip address to bind to, empty will bind to all interfaces
http_addr = 127.0.0.1

# The http port  to use
http_port = 3000

# The public facing domain name used to access grafana from a browser
domain = <your domain to access the grafana instance like: grafana.example.org>

# Redirect to correct domain if host header does not match domain
# Prevents DNS rebinding attacks
;enforce_domain = false

# The full public facing url you use in browser, used for redirects and emails
# If you use reverse proxy and sub path specify full url (with sub path)
root_url = %(protocol)s://%(domain)s:%(http_port)s/


# ...
[security]
# disable creation of admin user on first start of grafana
;disable_initial_admin_creation = false

# default admin user, created on startup
admin_user = admin

# default admin password, can be changed before first start of grafana,  or in profile settings
admin_password = """<insert a long and safe password here>"""


# ...
[users]
# disable user signup / registration
allow_sign_up = false

# Allow non admin users to create organizations
allow_org_create = false

# Set to true to automatically assign new users to the default organization (id 1)
auto_assign_org = true

# Set this value to automatically add new users to the provided organization (if auto_assign_org above is set to true)
;auto_assign_org_id = 1

# Default role new users will be automatically assigned (if disabled above is set to true)
;auto_assign_org_role = Viewer

# Require email validation before sign up completes
verify_email_enabled = true


# ...
[smtp]
enabled = true
host = <smtp address, you may use google smtp here or anything else>:<port, 587 for TLS, 465 for SSl (usually)>
user = <your email>@<your domain>
# If the password contains # or ; you have to wrap it with triple quotes. Ex """#password;"""
password = <smtp password>
;cert_file =
;key_file =
;skip_verify = false
from_address = <your email>@<your domain>
from_name = "Grafana"


# ...
[log]
# Either "console", "file", "syslog". Default is console and  file
# Use space to separate multiple modes, e.g. "console file"
;mode = console file

# Either "debug", "info", "warn", "error", "critical", default is "info"
level = error
```

This configuration will change the default password of Grafana to the one you define, avoid too much logging and secure the environment, remember to change all the placeholder _\<text>_

The SMTP setup is really important as it allows you to receive notifications when the server is running out of storage and/or ram.

Now that the configuration is tweaked it's time to start Grafana, run the following command to start it.

```
systemctl enable grafana-server
service grafana-server start
```

Finally, the last thing to do is link your domain to the Grafana internally running service, this can be done with the help of _nginx._ You may install and setup it using the following command

```
apt install nginx
cat > /etc/nginx/sites-available/grafana-reverse-proxy <<EOF
# this is required to proxy Grafana Live WebSocket connections.
map $http_upgrade $connection_upgrade {
  default upgrade;
  '' close;
}

server {
        server_name <your domain name to access the grafana instance like: grafana.example.org>;

        root /var/www/html;
        index index.html;

        location / {
          proxy_pass http://localhost:3000/;
          proxy_set_header Host $host;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection "upgrade";
        }

	location /api/live {
   		rewrite  ^/(.*)  /$1 break;
    		proxy_http_version 1.1;
    		proxy_set_header Upgrade $http_upgrade;
    		proxy_set_header Connection $connection_upgrade;
    		proxy_set_header Host $http_host;
    		proxy_pass http://localhost:3000/;
  	}

        listen [::]:443 ssl ipv6only=on default_server;
        listen 443 ssl default_server;
        ssl_certificate <your ssl certificate .pem>;
        ssl_certificate_key <your ssl certificate .key>;

        ssl_session_cache shared:cache_nginx_SSL:1m;
        ssl_session_timeout 1440m;

        ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
        ssl_prefer_server_ciphers on;

        ssl_ciphers "ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES256-SHA:ECDHE-ECDSA-DES-CBC3-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:DES-CBC3-SHA:!DSS";
}
EOF
ln -s /etc/nginx/sites-available/grafana-reverse-proxy /etc/nginx/sites-enabled/
rm /etc/nginx/sites-enabled/default
```

In order to complete the setup we must place Nginx under supervisor in order to avoid its shutdown if any error occurs, you can do it running the following commands

```
cat > /etc/supervisor/conf.d/nginx.conf <<EOF
[program:nginx]
command=/usr/sbin/nginx -g "daemon off;"
autostart=true
autorestart=true
startretries=5
numprocs=1
stderr_logfile=/var/log/supervisor/%(program_name)s.log
stderr_logfile_maxbytes=10MB
stdout_logfile=/var/log/supervisor/%(program_name)s.error.log
stdout_logfile_maxbytes=10MB
EOF
systemctl disable nginx
service nginx stop
supervisorctl reread
supervisorctl update
supervisorctl status
```

After you run all the previous commands you will have enabled Supervisor to automatically start Nginx at startup and actually run Nginx. You can now navigate to your domain where Grafana is hosted (like: grafana.example.org) and log in using the credentials you previously set up.

Once logged in to set up your dashboards you have to create first a data source navigating to _Configuration_ and choosing _Datasource,_ create a new Prometheus data source and as the URL insert `http://localhost:9090` then save and test.&#x20;

To import the dashboards navigate to the plus button on the navigation panel and select import, then proceed with the import of a JSON file. We've chosen to provide you the best dashboards, simply download the JSON provided below and import on your Grafana instance to start visualizing your data.

{% file src="../.gitbook/assets/Node metrics-1641061881597 (1).json" %}
Server node metrics
{% endfile %}

{% file src="../.gitbook/assets/Melodity beats node- Metrics-1641061809612.json" %}
Melodity beats node metrics
{% endfile %}
