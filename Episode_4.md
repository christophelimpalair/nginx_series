#Nginx for dynamic PHP sites (with PHP-FPM)

/etc/nginx/sites-available/test.com

```
upstream backend { # Lets you define a group of servers
	server unix:/var/run/php5-fpm.sock;
}

server {
	listen 80;

	root /usr/share/nginx/test;
	index index.php;

	server_name test.com;
 
 	# We keep this block, because it doesn't make sense to pass
	# everything to PHP.
	location / {
		try_files $uri $uri/ =404;
	} 

	location ~ \.php$ {
	  fastcgi_split_path_info ^(.+\.php)(/.+)$; # this defines a regular expression to separate the SCRIPT_FILENAME and PATH_INFO for later use
	  try_files $uri =404; # OR Set cgi.fix_pathinfo=0 in php.ini (for security)
	  fastcgi_pass backend; # OR unix:/var/run/php5-fpm.sock OR 127.0.0.1:9000
	  fastcgi_index index.php; # appends index.php to a URI ending with a slash
	  include fastcgi_params;
	}

}
```

Keep in mind this is as basic as it gets. There are other settings you can have. For example:

```
# Lets you define a group of servers to distribute the load
# By default, uses a round-robin method of distributing load
upstream backend {
	server unix:/var/run/php5-fpm.sock 	weight=3;
	server backend1.example.com:8080	max_fails=2 fail_timeout=30s;

	server backup1.example.com 			backup;
}
```

Create a PHP file at the **root** directory you chose. In this case, **/usr/share/nginx/test**

```
sudo vim /usr/share/nginx/test/index.php
```

```
<?php

echo "Hi, I am PHP";

?>
```

Once you have your Nginx config and a PHP file, it's time to install PHP-FPM

```
sudo apt-get update
```

```
sudo apt-get install php5-fpm -y
```

You can verify that it is running with

```
ps aux | grep php-fpm
```

**ps:** report a snapshot of the current processes

**a:** shows all processes for all users

**u:** show the process's user/owner

**x:** shows processes not attached to a terminal

Then, make your own configuration file:

```
sudo vim /etc/php5/fpm/pool.d/test.conf
```

```
[app]
listen = /var/run/php5-fpm.sock
user = www-data
group = www-data
listen.owner = www-data
listen.group = www-data
pm = ondemand
pm.max_children = 5
pm.max_requests = 500
```

**Note:** the *pm = ondemand* and *pm.max_children = 5* and *pm.max_requests = 500* are probably not the best options for your needs. Learn what they do and how to change them here:

https://scaleyourcode.com/blog/article/2

(Search for it with ctrl+f)

**Also note**: The default (/etc/php5/fpm/pool.d/www.conf) configuration file that comes with PHP5-FPM listens on the same socket, so you have to either move/delete that file or use a different socket, or else you will have a conflict and PHP-FPM won't work right.

Good, now let's restart Nginx and PHP-FPM

```
sudo service nginx restart
```

```
sudo service php5-fpm restart
```

```
curl localhost
```

returns: "Hi, I am PHP!"

##Troubleshooting
If you run in any errors restarting these services, or if curl doesn't give you what you were expecting, then check each service's logs.

```
sudo vim /var/log/nginx/error.log
```

```
sudo vim /var/log/php5-fpm.log
```
