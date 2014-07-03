## Solr/Nginx Reverse Proxy

### Proxy Solr with Nginx

While a lot of proxies for Solr exist for environments like [Node](https://www.npmjs.org/package/solr-security-proxy) its often preferable to do our best to push proxy responsibilities to a full web server whenever possible. This approach takes full advantage of the capabilities of the web server to layer in features such as SSL and basic auth as needed.

This repository gives a basic outline to creating a functional reverse proxy with Nginx that allows a whitelist of specific Solr request handlers and disallows specific query params (qt, stream.*, etc).

### General Usage Approach

Our general approach to deploying Solr is

1. Start Solr with Jetty out of the unmodified default 'example' directory. Something like

```
java -jar -Dsolr.solr.home=/path/to/solr/confs -Djetty.host=127.0.0.1 start.jar
```

(even though this is called "example" its really a basic starter Jetty install for Solr)

2. Take the solr.conf from this repo and proxy with your info as comments dictate in the solr.conf file in this repo

3. Use Nginx with modified conf files from this repo to proxy Solr to the rest of the world

On Ubuntu, this looks like:
```
sudo cp solr.conf /etc/nginx/conf.d/
sudo cp solr_proxy.conf /etc/nginx/conf.d/
sudo /etc/init.d/nginx restart
```

4. Confirm you can only access specific handlers with your browser

5. SSH tunnel into the box to access solr admin, etc

```
ssh -L8983:localhost:8983 your.solr.com 
```
