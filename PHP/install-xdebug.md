# How to install XDebug on OSX

XDebug is a PHP extension which provides debugging and profiling capabilities.

Installation is fairly painless on OSX.

### Assumptions

* Working PHP installation
* pecl is installed and functional
* Comfortable with CLI and vim

### Step 1:  Install the xdebug extension

    $ sudo pecl install xdebug
    
* Note: Ignore the pecl prompt to add "extension=xdebug.so" to your php.ini - this will cause problems. *

### Step 2:  Configure PHP to utilize xdebug

Search the php.ini to find "zend_extnsion".  In mine the line to enable xdebug already existed and just needed to be uncommented (remove the ;).  If not add the following line.

    zend_extension="/usr/lib/php/extensions/no-debug-non-zts-20090626/xdebug.so"

### Step 3:  Restart Apache

    $ sudo apachectl -k restart