# Install from Package
sudo apt-get update

sudo apt-get install nginx

Removing Nginx package installation (if you need to, for whatever reason. I just did this to install from source)
sudo apt-get remove --purge nginx* #remove Nginx



# Install from source 
(This is the latest stable version as of right now. There are newer, development, versions)
wget "http://nginx.org/download/nginx-1.8.0.tar.gz" 

tar -zxvg nginx-1.8.0.tar.gz

cd nginx-1.8.0

sudo apt-get update

sudo apt-get install build-essential  #build tools

sudo apt-get install libpcre3-dev zlib1g-dev libssl-dev  # Nginx dependencies

./configure --with-http_ssl_module \  #enable ssl
		--with-http_realip_module \  #forward real user IP in a load balancer situation
		--with-http_stub_status_module  #enable nginx statistics (like active connections and more)

make

sudo make install

/usr/local/nginx/sbin/nginx -V # Check that it’s installed, and which version we have

#### Download nginx startup script
wget -O init-deb.sh https://www.linode.com/docs/assets/660-init-deb.sh

( Alternate startup script: http://wiki.nginx.org/InitScripts )

#### Move script to the init.d directory and make executable
sudo mv init-deb.sh /etc/init.d/nginx
sudochmod +x /etc/init.d/nginx

#### Add nginx to the system startup
sudo /usr/sbin/update-rc.d -f nginx defaults

