# PHP 
[PHP](http://www.php.net) is a popular general-purpose scripting language that is especially suited to web development.

Fast, flexible and pragmatic, PHP powers everything from your blog to the most popular websites in the world.

### Homebrew install 
	This proccess relies heavly on the macOS package manager called [Homebrew](https://github.com/Homebrew). Using the brew command you can easily add powerful functionality to your mac, but first we have to install it. This is a simple process, but you need to launch your Terminal (/Applications/Utilities/Terminal) application and then enter.

	$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
	
	Just follow the terminal prompts and enter your password where required. This will install Homebrew and also install the required XCode Command Line Tools if you don't already have XCode installed. This may take a few minutes, but when complete, a quick way to ensure you have installed brew correctly, simply type:
	$ brew --version
	$ brew doctor

### Homebrew Extra Brew Taps

	We are going to use some brews that require some external taps:

	$ brew tap homebrew/dupes
	$ brew tap homebrew/versions
	$ brew tap homebrew/php
	$ brew tap homebrew/apache
	If you already have brew installed, make sure you have the all the latest available brews:

	$ brew update
	Now you are ready to brew!


### Homebrew method

    $ brew install php56

需要PHP多版本并存，可切换。
在安装完5.6版本，之后，我们要继续安装一个7.0版本，首先要unlink当前的版本
	$ brew unlink php56
    $ brew install php70


### php-version manager
	通过brew安装的php可以通过brew link和brew unlink来切换不同版本。例如，
	$ brew list
	$ brew unlink php56
	$ brew link php55

	php-version是一个帮助管理从brew安装的php版本切换的工具。

	安装非常简单

	$ brew install php-version
	$ source $(brew --prefix php-version)/php-version.sh && php-version 5
	$ php-version

		* 5.6.18
		  7.0.14

### php-version config

在nginx的配置文件中声明使用哪个版本的PHP，接下来，进入PHP的配置目录把php-fpm的端口默认端口修改掉。

5.6的配置文件在/usr/local/etc/php/5.6/php-fpm.conf
7.0的配置文件在/usr/local/etc/php/7.0/php-fpm.d/www.conf
修改这两个配置文件查找9000字样，修改成不同的端口，我这里修改成了9056和9070端口

修改ngxin的配置文件

	$ fastcgi_pass 127.0.0.1:9056;
	$ fastcgi_pass 127.0.0.1:9070;

然后同时启动两个php-fpm

1 /usr/local/Cellar/php56/5.6.29_5/sbin/php56-fpm start
2 /usr/local/Cellar/php70/7.0.14_7/sbin/php70-fpm start


重启nginx。

如果想关掉所有php-fpm进程，就执行

	$ sudo killall php-fpm

### php options install
	You can get a full list of available options to include by typing,
	$ brew options php56

	for example, In this example we are just including Apache support via --with-httpd24 to build the required PHP modeules for Apache
	$ brew install php56 --with--httpd24
	
	/usr/local/opt/php56-intl/intl.so
	/usr/local/opt/php56-intl/intl.so

### How to install PHP intl extension on OS X
	
	Prerequistite
	Homebrew
	PECL
	Terminal

	Install ICU Libraries via Homebrew
	$ brew update
	$ brew install icu4c

	Install Intl Extension via PECL
	$ sudo pecl update-channels
	$ sudo pecl install intl

	or
	$ brew reinstall --build-from-source php56-intl
	$ brew reinstall --build-from-source php57-intl

	$ brew install --build-from-source php70-intl
	$ brew install --build-from-source php56-intl













