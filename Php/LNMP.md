# LNMP


### Install php-fpm and nginx


	$ brew tap homebrew/dupes
	$ brew tap homebrew/versions
	$ brew tap homebrew/php

	brew install php56 \
	--without-snmp \
	--without-apache \
	--with-debug \
	--with-fpm \
	--with-intl \
	--with-homebrew-curl \
	--with-homebrew-libxslt \
	--with-homebrew-openssl \
	--with-imap \
	--with-mysql \
	--with-tidy

	$ brew install php56-gearman php56-msgpack php56-memcache php56-memcached php56-mongo  php56-phalcon php56-redis php56-xdebug

	$ brew install nginx

### Config Nginx




