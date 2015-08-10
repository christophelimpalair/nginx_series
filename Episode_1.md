# Install from Package
```
sudo apt-get update
```

```
sudo apt-get install nginx
```

#### Removing Nginx package installation (if you need to, for whatever reason. I just did this to install from source)

Remove Nginx installation

```
sudo apt-get remove --purge nginx* 
```


# Install from source 
(This is the latest stable version as of right now. There are newer, development, versions)

```
wget "http://nginx.org/download/nginx-1.8.0.tar.gz" 
```

```
tar -zxvg nginx-1.8.0.tar.gz
```

```
cd nginx-1.8.0
```

#### Build tools
```
sudo apt-get update
```

```
sudo apt-get install build-essential
```

#### Nginx dependencies
```
sudo apt-get install libpcre3-dev zlib1g-dev libssl-dev
```


###### Enable SSL --with-http_ssl_module
###### Forward real user IP (in a load balancer situation for example) --with-http_realip_module
###### Enable nginx statistics (Like active connections and more) --width-http_stub_status_module
```
./configure --with-http_ssl_module \
		--with-http_realip_module \
		--with-http_stub_status_module
```

```
make
```

```
sudo make install
```

#### Check that it’s installed, and which version we have
```
/usr/local/nginx/sbin/nginx -V
```

#### Download nginx startup script
```
wget -O init-deb.sh https://www.linode.com/docs/assets/660-init-deb.sh
```

( Alternate startup script: http://wiki.nginx.org/InitScripts )

#### Move script to the init.d directory and make executable
```
sudo mv init-deb.sh /etc/init.d/nginx
```

```
sudochmod +x /etc/init.d/nginx
```

#### Add nginx to the system startup
```
sudo /usr/sbin/update-rc.d -f nginx defaults
```
