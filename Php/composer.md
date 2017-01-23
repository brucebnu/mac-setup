# Composer Usage

[packagist](https://packagist.org/)
[getcomposer](https://getcomposer.org/)
[github composer](https://github.com/composer/composer)

[中国全量镜像](http://pkg.phpcomposer.com/)
	
	$ php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
	$ php -r "if (hash_file('SHA384', 'composer-setup.php') === '55d6ead61b29c7bdee5cccfb50076874187bd9f21f65d8991d46ec5cc90518f447387fb9f76ebae1fbbacf329e583e30') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
	$ php composer-setup.php
	$ php -r "unlink('composer-setup.php');"

	or
    $ brew install composer

    or
    $ curl -sS https://getcomposer.org/installer | php
	$ mv composer.phar /usr/local/bin/composer

To config of composer
	show composer envionment variablesß
	$ composer config -l -g

	[home] ~/.composer is config save path

	$ vim ~/.composer/config.json

添加国内镜像地址
	{
	    "repositories": [
	        {"type": "composer", "url": "http://pkg.phpcomposer.com/repo/packagist/"},
	        {"packagist": false}
	    ]
	}

	更新Composer
	$ composer self-update


To upgrade a package:
    $ composer update -vvv

To see what's installed:
	entren info have composer.json of DIR
    $ composer install

To uninstall a package:



### Troubleshooting Tips
    Failed to decode response: zlib_decode(): data error
Retrying with degraded mode, check https://getcomposer.org/doc/articles/troubleshooting.md#degraded-mode for more info
	$ composer clear-cache

	installing with debug verbosity 
	$ composer install -vvv

