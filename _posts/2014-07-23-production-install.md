---
layout: page
title: "Production Install"
category: start
date: 2014-07-23 15:02:05
order: 3
---

### Notes
* This documentation assumes the production has one Linux-based server. The code and documentation provided is tailored for a monolithic install, however in a full production environment DigitalNZ runs a cluster of front-end services delivering the public API, supported by a back-end service dedicated to harvesting.
* Replace `example.com` with your own domain name

### 1. Install Ruby, Redis and MongoDB

See the [Dependencies](/supplejack/start/dependencies.html) for instructions and versions.

### 2. Install Supplejack Applications

Installing the applications and configuring them can be quite tricky. Refer to the [application template code](https://github.com/DigitalNZ/supplejack_installation/blob/master/supplejack_api_template.rb) to see what we do if you get stuck.

The Rails applications need to be installed in separate folders which will be referred in the Apache configuration. Typically this is a mounted volume, so could be something like `/data/sites/supplejack_manager`.

1. Clone the Manager from git via `git clone git@github.com:DigitalNZ/supplejack_manager.git`.
1. Run `bundle install` from the `supplejack_manager` directory.
1. Clone the Worker from git via `git clone git@github.com:DigitalNZ/supplejack_worker.git`
1. Run `bundle install` from the `supplejack_worker` directory.
1. Clone **your** API which includes the Supplejack API Engine from **your git repository**.
1. Run `bundle install` from the `api` directory.

### 3. Generate User API Keys

The applications orchestrate harvesting activities by communicating over Restful JSON APIs. In order for this work, user API keys need to be created in each application. The API keys is a random assortment of numbers and letters, like `RhymLHa9xRQGU8gyAYXP`. Perform the following:

Generate the associated keys for the Worker and the Manager as described in Steps Three, Four, and Five [here](/supplejack/start/installation-walk-through-by-example.html).

### 4. Setup Supplejack Manager

Refer to the [Supplejack Manager section](/supplejack/start/installation-walk-through-by-example.html).

For the `WORKER_HOST` and `API_HOST` use the external address you intend to host these applications as, such as `worker.example.com` and `api.example.com`. We will setup this configuration in Apache later.

### 5. Setup Supplejack Worker

Refer to the [Supplejack Worker section](/supplejack/start/installation-walk-through-by-example.html).

You need to:

1. Create an Environment Configuration in `application.yml`, based on the example.
1. Start [Sidekiq](http://sidekiq.org) via `bundle exec sidekiq start` from the worker directory

For the `MANAGER_HOST` and `API_HOST` use the external address you intend to host these applications as, such as `manager.example.com` and `api.example.com`. We will setup this configuration in Apache later.

### 6. Setup Supplejack API

There is nothing to configure for Supplejack API as the configuration is inside **your** API (which contains the `supplejack_api` engine). However, you may need to setup any configuration needed for your application.

### 7. Install Java Development Kit (JDK)

[Apache Solr](http://lucene.apache.org/solr/) requires a Java Runtime environment to be installed. We are currently using Java 8 with Solr5.

Install Oracle JDK for your platform. See [Oracle's Website](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) for details

### 8. Install Solr

We recommend using Solr5, please read the Getting Started and Taking Solr to Production information on their documentation for information on how to get up and running.

http://archive.apache.org/dist/lucene/solr/ref-guide/apache-solr-ref-guide-5.5.pdf

### 9. Install and configure Apache with Passenger

1. Install Apache if not already installed.
1. Install the [Phusion Passenger module](https://www.phusionpassenger.com/) into Apache to run Rails applications.
1. Create `VirtualHost` configurations for the manager, worker, API and Solr:

#### Manager:
```apacheconf
<VirtualHost HOST_IP_ADDRESS:80>
  DocumentRoot /data/sites/manager.example.com/httpdocs
  ServerName manager.example.com
  ServerAlias www.manager.example.com

  PassengerAppRoot /data/sites/<YOUR_MANAGER_DIRECTORY>/

  RailsEnv production
  RackEnv production

  <Directory /data/sites/<YOUR_MANAGER_DIRECTORY>/httpdocs>
    AllowOverride All

    Order allow,deny
    Allow from all
  </Directory>

  ErrorLog /data/sites/<YOUR_MANAGER_DIRECTORY>/logs/error_log
  CustomLog /data/sites/<YOUR_MANAGER_DIRECTORY>/logs/access_log combined

  RewriteEngine On
  RewriteCond %{HTTP_HOST} ^www.manager.example.com$
  RewriteRule ^(.*)$ http://manager.example.com$1 [L,R=301]

  Header always unset X-Powered-By
  Header always unset X-Runtime
</VirtualHost>
```
#### Worker:
```apacheconf
<VirtualHost HOST_IP_ADDRESS:80>
  DocumentRoot /data/sites/<YOUR_WORKER_DIRECTORY>/httpdocs
  ServerName worker.example.com
  ServerAlias www.worker.example.com

  PassengerAppRoot /data/sites/<YOUR_WORKER_DIRECTORY>/

  RailsEnv production
  RackEnv production

  <Directory /data/sites/<YOUR_WORKER_DIRECTORY>/httpdocs>
    AllowOverride All

    Order allow,deny
    Allow from all
  </Directory>

  ErrorLog /data/sites/<YOUR_WORKER_DIRECTORY>/logs/error_log
  CustomLog /data/sites/<YOUR_WORKER_DIRECTORY>/logs/access_log combined

  RewriteEngine On
  RewriteCond %{HTTP_HOST} ^www.worker.example.com$
  RewriteRule ^(.*)$ http://worker.example.com$1 [L,R=301]

  Header always unset X-Powered-By
  Header always unset X-Runtime
</VirtualHost>
```
#### API:
```apacheconf
<VirtualHost HOST_IP_ADDRESS:80>
  DocumentRoot /data/sites/<YOUR_API_DIRECTORY>/httpdocs
  ServerName api.example.com
  ServerAlias www.api.example.com

  ProxyPass / http://HOST_IP_ADDRESS:81/ retry=0
  ProxyPassReverse / http://HOST_IP_ADDRESS:81/

  <Proxy *>
    Order allow,deny
    Allow from all
  </Proxy>

  ErrorLog /data/sites/<YOUR_API_DIRECTORY>/logs/error_log
  CustomLog /data/sites/<YOUR_API_DIRECTORY>/logs/access_log combined
</VirtualHost>
```
#### Solr:
```apacheconf
<VirtualHost HOST_IP_ADDRESS:80>
  DocumentRoot /data/sites/<YOUR_SOLR_DIRECTORY>/httpdocs
  ServerName solr.example.com
  ServerAlias www.solr.example.com

  ProxyPass / http://HOST_IP_ADDRESS:8080/ retry=0
  ProxyPassReverse / http://HOST_IP_ADDRESS:8080/

  <Proxy *>
    Order allow,deny
    Allow from all
  </Proxy>

  ErrorLog /data/sites/<YOUR_SOLR_DIRECTORY>/logs/error_log
  CustomLog /data/sites/<YOUR_SOLR_DIRECTORY>/logs/access_log combined
</VirtualHost>
```

### 10. Run Thin servers for the API

[Thin](http://code.macournoyer.com/thin/) is a lightweight Ruby web server. We suggest that you run 8 `thin` worker processes to handle API traffic:

```bash
$ sudo /data/sites/api.example.com/bin/thin start -d -u www-data -g www-data -p 8190 -s 8 -e production --stats='/t_stats'
```

Note: API traffic isn't handled by Apache, it just provides a reverse proxy.

### 11. Install HAProxy

[HAProxy](http://www.haproxy.org/) is a HTTP load-balancer. We use it to distribute the load across the 8 `thin` instances we created above, exposing them a single server running on port `81`.

1. Install HAProxy v`1.4.24`
1. Ensure you can access http://HOST:22002
1. Add this section to the HAPRoxy configuration (`/etc/haproxy/haprpoxy.cfg`) to:

```
listen application 0.0.0.0:81
  balance  roundrobin
  option forwardfor

  stats enable
  stats refresh 5s

  server thin8190 127.0.0.1:8190 weight 1 check
  server thin8191 127.0.0.1:8191 weight 1 check
  server thin8192 127.0.0.1:8192 weight 1 check
  server thin8193 127.0.0.1:8193 weight 1 check
  server thin8194 127.0.0.1:8194 weight 1 check
  server thin8195 127.0.0.1:8195 weight 1 check
  server thin8196 127.0.0.1:8196 weight 1 check
  server thin8197 127.0.0.1:8197 weight 1 check
```

### 12. Networking

1. You may need to configure firewalls to allow you to access services and hosts
1. You might need to configure DNS servers or `/etc/hosts` to allow you to access hosts as names, such as `api.example.com`

### 13. Testing

1. Ensure MongoDB running via `mongo`
1. Ensure Solr is running at http://api.example.com:8080
1. Ensure that the API is running at http://api.example.com/records/1.json?api_key=API_KEY
1. Ensure that Sidekiq for API is running at http://api.example.com/sidekiq
1. Ensure that Sidekiq for worker is running at http://worker.example.com/sidekiq
1. Ensure that the manager is running at http://manager.example.com
1. Ensure that the worker is running at http://worker.example.com
