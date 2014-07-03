## Solr/Nginx Reverse Proxy

### Proxy Solr with Nginx

While a lot of proxies for Solr exist for environments like [Node](https://www.npmjs.org/package/solr-security-proxy) its often preferable to do our best to push proxy responsibilities to a full web server. This approach takes full advantage of the capabilities of the web server to layer in features such as SSL or basic auth.

This repository gives a basic outline to creating a functional reverse proxy with Nginx that allows a whitelist of specific Solr request handlers and disallows specific query params (qt, stream.*, etc).

### Why a reverse proxy? Why not just muck with Jetty configs in example? Or deploy to Tomcat or something?

Solr is most thoroughly tested with Jetty. Specifically, Jetty as bundled with Solr in the "example" directory. At OpenSource Connections, we frequently need to help clients troubleshoot obscure bugs using Solr in other servlet containers. What we encourage clients to do is to run Solr with Jetty using the configuration in the "example" directory (dutifully explaining that "example" should probably really be called "default").

However, the default Jetty distributed with Solr (the "example" dir) is not at all locked down. Security is not a concern. One solution would be to start mucking with the Jetty configuration to layer in SSL, disallow different endpoints, enable authentication, etc. Unfortunately, once this starter "example" directory is laden with added configuration, it becomes harder to just upgrade Solr in-place. We can no longer easily "rip and replace" the example directory. Instead we need to replicate any configuration we need when upgrading.

Rather, this approach allows a clear seperation of concerns with a vanilla Jetty/Solr "example" default directory fronted by a robust and secure web server like Nginx. This lets users run Solr as they're used to (java -jar ... start.jar) without mucking with much and allows tight control over security as needed.

### General Solr Deployment Approach

Our general approach to deploying Solr is

#### 1. Start Solr with Jetty out of the unmodified default 'example' directory. Something like

```
java -jar -Dsolr.solr.home=/path/to/solr/confs -Djetty.host=127.0.0.1 start.jar
```

#### 2. Take the solr.conf from this repo and proxy with your info as comments dictate in the solr.conf file in this repo

#### 3. Use Nginx with modified conf files from this repo to proxy Solr to the rest of the world

On Ubuntu, this looks like:
```
sudo cp solr.conf /etc/nginx/conf.d/
sudo cp solr_proxy.conf /etc/nginx/conf.d/
sudo /etc/init.d/nginx restart
```

#### 4. Confirm you can only access specific handlers with your browser.

Double check you can't take disallowed actions like updated documents and the like.

#### 5. SSH tunnel into the box to access solr admin, etc

```
ssh -L8983:localhost:8983 your.solr.com 
```

### TODO
1. Demonstrate basic auth for update handlers and /solr 
2. Demonstrate SSL
