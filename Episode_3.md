# Nginx Multi & Single Site Configuration (Static Sites)
If you installed Nginx with:

```
sudo apt-get install nginx
```

Then you already have a default site configuration. In which case:

```
curl localhost
```

Will return an HTML page telling you that Nginx was successfully installed.

But we want our own, so let's create it (or them).

First, let's create a new directory for this site.
```
sudo mkdir /usr/share/nginx/test.com
```

And let's add an HTML page to it.
```
sudo vim /usr/share/nginx/test.com/index.html

<h1>hi, testing Nginx.</h1>

:x
```

Now we need to configure this site in Nginx.
```
sudo vim /etc/nginx/sites-available/test.com
```
```
server {
	listen 80;

	root /usr/share/nginx/test.com;
	index index.html index.htm;

	server_name test.com;

	location / {
		try_files $uri $uri/ =404;
	}

}

:x
```
Now, we need to create a symlink from our sites-available/test.com to sites-enabled/test.com (http://man7.org/linux/man-pages/man7/symlink.7.html)

Otherwise, Nginx will not actively use this configuration file.

```
sudo ln -s /etc/nginx/sites-available/test.com /etc/nginx/sites-enabled/test.com
```

If you **ls** the sites-enabled directory, you should see **test.com**

This next step is dependent on whether you own the domain name or not. If you do, skip this.
If you don't actually own it, and you are doing this to test, then this step is required.

```
sudo vim /etc/hosts
```
Add this line below the other ones that point to 127.0.0.1:
```
127.0.0.1	localhost	test.com	www.test.com
```
This will route test.com to localhost:80 when we curl.

Time to restart Nginx!
```
sudo service nginx restart
```
```
curl test.com
```
