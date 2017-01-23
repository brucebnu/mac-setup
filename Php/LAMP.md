# LAMP 

LAMP is Linux Apache server Mysql and PHP


### Apache install
	The latest macOS 10.12 Sierra comes with Apache 2.4 pre-installed, however, it is no longer a simple task to use this version with Homebrew because Apple has removed some required scripts in this release. However, the solution is to install Apache 2.4 via Homebrew and then configure it to run on the standard ports (80/443).

	If you already have the built-in Apache running, it will need to be shutdown first, and any auto-loading scripts removed. It really doesn't hurt to just run all these commands in order - even if it's a fresh installation:
	$ sudo apachectl stop
	$ sudo launchctl unload -w /System/Library/LaunchDaemons/org.apache.httpd.plist 2>/dev/null

	Apache is not in the default repository of Homebrew formulas, nor are some dependencies, so we use the brew tap command to add other formula repositories.
	$ brew tap homebrew/dupes
	$ brew tap homebrew/apache

	$ brew install httpd24 --with-privileged-ports --with-http2 --with-brewed-openssl

	This step takes a little while as it builds Apache from source. Upon completion you should see a message like:
	🍺  /usr/local/Cellar/httpd24/2.4.23_2: 212 files, 4.4M, built in 1 minute 45 seconds

	This is important because you will need that path in the next step. In this example the path was /usr/local/Cellar/httpd24/2.4.23_2. If you get a newer version, simply use that path in the next line:
	$ sudo cp -v /usr/local/Cellar/httpd24/2.4.23_2/homebrew.mxcl.httpd24.plist /Library/LaunchDaemons
	$ sudo chown -v root:wheel /Library/LaunchDaemons/homebrew.mxcl.httpd24.plist
	$ sudo chmod -v 644 /Library/LaunchDaemons/homebrew.mxcl.httpd24.plist
	$ sudo launchctl load /Library/LaunchDaemons/homebrew.mxcl.httpd24.plist

### Troubleshooting Tips

	If you get a message that the browser can't connect to the server, first check to ensure the server is up.

	$ ps -aef | grep httpd
	You should see a few httpd processes if Apache is up and running.

	Try to restart Apache with:

	$ sudo apachectl -k restart
	You can watch the Apache error log in a new Terminal tab/window during a restart to see if anything is invalid or causing a problem:

	$ tail -f /usr/local/var/log/apache2/error_log

	If that doesn't work, check to ensure you have Listen: 80 in your 
	$ vim /usr/local/etc/apache2/2.4/httpd.conf
	configuration file.

	Apache is controlled via the apachectl command so some useful commands to use are:
	$ sudo apachectl start
	$ sudo apachectl stop
	$ sudo apachectl -k restart
	
	The -k will force a restart immediately rather than asking politely to restart when apache is good and ready


### Apache config
	[Apache HTTP Server Wiki](https://wiki.apache.org/httpd)
	This is a wiki containing user-contributed recipes, tips, and tricks for the Apache HTTP Server (aka Apache Web Server or httpd). 

	As this is a public wiki, please remember to consult the [official documentation](http://httpd.apache.org/docs/2.4/) to verify what you read here. (If something here breaks your website, sorry!) Nevertheless, we hope you find this information useful! 

	let's assume we want to change the document root to point to a folder in our own home directory. To do this, we will need to edit Apache's configuration file.

	$ /usr/local/etc/apache2/2.4/httpd.conf

	search for the term DocumentRoot, and you should see the following line:
	$ DocumentRoot "/usr/local/var/www/htdocs"

	Change this to point to your user directory where your_user is the name of your user account:
	$ DocumentRoot /Users/your_user/Sites

	You also need to change the <Directory> tag reference right below the DocumentRoot line. This should also be changed to point to your new document root also:
	$ <Directory /Users/your_user/Sites>

	In that same <Directory> block you will find an AllowOverride setting, this should be changed as follows:
	# AllowOverride controls what directives may be placed in .htaccess files.
	# It can be "All", "None", or any combination of the keywords:
	#   AllowOverride FileInfo AuthConfig Limit
	#
	AllowOverride All

	Also we should now enable mod_rewrite which is commented out by default. Search for mod_rewrite.so and uncomment the line by removing the leading #:
	$ LoadModule rewrite_module libexec/mod_rewrite.so

	User & Group

	Now we have the Apache configuration pointing to a Sites folder in our home directory. One problem still exists, however. By default, apache runs as the user daemon and group daemon. This will cause permission problems when trying to access files in our home directory. About a third of the way down the httpd.conf file there are two settings to set the User and Group Apache will run under. Change these to match your user account (replace your_user with your real username), with a group of staff:
	$ User your_user
	$ Group staff

	Sites Folder
	$ mkdir ~/Sites
	$ echo "<h1>This is my website. Hello World</h1>" > ~/Sites/index.html

	Restart apache to ensure your configuration changes have taken effect:
	$ sudo apachectl -k restart

### To build the required PHP modules for Apache
	We will proceed by installing PHP 5.5, PHP 5.6, PHP 7.0, and PHP 7.1 and using a simple script to switch between them as we need.

	You can get a full list of available options to include by typing brew options php55, for example. In this example we are just including Apache support via --with-httpd24 to build the required PHP modules for Apache.
	$ brew install php55 --with-httpd24
	$ brew unlink php55
	$ brew install php56 --with-httpd24
	$ brew unlink php56
	$ brew install php70 --with-httpd24
	$ brew unlink php70
	$ brew install php71 --with-httpd24
	This may take some time as your computer is actually compiling PHP from source.

	You must reinstall each PHP version with reinstall command rather than install if you have previously installed PHP through Brew.

	Also, you may have the need to tweak configuration settings of PHP to your needs. A common thing to change is the memory setting, or the date.timezone configuration. The php.ini files for each version of PHP are located in the following directories:

	/usr/local/etc/php/5.5/php.ini
	/usr/local/etc/php/5.6/php.ini
	/usr/local/etc/php/7.0/php.ini
	/usr/local/etc/php/7.1/php.ini


### Apache PHP Setup
	You have successfully installed your PHP versions, but we need to tell Apache to use them. You will again need to edit the /usr/local/etc/apache2/2.4/httpd.conf file and search for #LoadModule php5_module.

	You will notice that each PHP version added a LoadModule entry, however these are all pointing to very specific versions. We can replace these with some more generic paths (exact versions may differ):

	LoadModule php5_module        /usr/local/Cellar/php55/5.5.38_11/libexec/apache2/libphp5.so
	LoadModule php5_module        /usr/local/Cellar/php56/5.6.26_3/libexec/apache2/libphp5.so
	LoadModule php7_module        /usr/local/Cellar/php70/7.0.11_5/libexec/apache2/libphp7.so
	LoadModule php7_module        /usr/local/Cellar/php71/7.1.0-rc.3_8/libexec/apache2/libphp7.so

	Modify the paths as follows:

	LoadModule php5_module    /usr/local/opt/php55/libexec/apache2/libphp5.so
	LoadModule php5_module    /usr/local/opt/php56/libexec/apache2/libphp5.so
	LoadModule php7_module    /usr/local/opt/php70/libexec/apache2/libphp7.so
	LoadModule php7_module    /usr/local/opt/php71/libexec/apache2/libphp7.so

	We can only have one module processing PHP at a time, so for now, comment out all but the php56 entry:
	#LoadModule php5_module    /usr/local/opt/php55/libexec/apache2/libphp5.so
	LoadModule php5_module    /usr/local/opt/php56/libexec/apache2/libphp5.so
	#LoadModule php7_module    /usr/local/opt/php70/libexec/apache2/libphp7.so
	#LoadModule php7_module    /usr/local/opt/php71/libexec/apache2/libphp7.so

	This will tell Apache to use PHP 5.6 to handle PHP requests. (We will add the ability to switch PHP versions later).

	Also you must set the Directory Indexes for PHP explicitly, so search for this block:

	<IfModule dir_module>
	    DirectoryIndex index.html
	</IfModule>

	and replace it with this:

	<IfModule dir_module>
	    DirectoryIndex index.php index.html
	</IfModule>

	<FilesMatch \.php$>
	    SetHandler application/x-httpd-php
	</FilesMatch>
	Save the file and restart Apache again, now that we have installed PHP:

	$ sudo apachectl -k restart

	Validating php Installation
	$ <?php phpinfo();
	Point your browser to http://localhost/phpinfo.php

### Apache install module


### Mysql Manager
	$ brew install mysql

	$ unset TMPDIR
	$ mysql_install_db --verbose --user=`whoami` --basedir="$(brew --prefix mysql)" --datadir=/usr/local/var/mysql --tmpdir=/tmp
	
	{start|stop|restart|reload|force-reload|status}
	$ mysql.server start

	$ mysqld --help --verbose

	$ mysqld --help --verbose | grep my.cnf

	$ mysqld --help --verbose | grep datadir
	/usr/local/var/mysql/

	$ sudo cp $(brew --prefix mysql)/support-files/my-default.cnf /etc/my.cnf


	错误日志默认会存在数据目录下，也就是上面所定义的 /usr/local/var/mysql/，如果 Mac 电脑名字是 MacBook，那日志的全路径就是 /usr/local/var/mysql/MacBook.local.err

	/bin/sh /usr/local/opt/mysql55/bin/mysqld_safe --bind-address=127.0.0.1 --datadir=/usr/local/var/mysql55

	/usr/local/Cellar/mysql55/5.5.44/bin/mysqld --basedir=/usr/local/Cellar/mysql55/5.5.44 --datadir=/usr/local/var/mysql55 --plugin-dir=/usr/local/Cellar/mysql55/5.5.44/lib/plugin --bind-address=127.0.0.1 --log-error=/usr/local/var/mysql55/BruceNius-MacBook-Pro.local.err --pid-file=/usr/local/var/mysql55/BruceNius-MacBook-Pro.local.pid --socket=/tmp/mysql.sock --port=3306






























