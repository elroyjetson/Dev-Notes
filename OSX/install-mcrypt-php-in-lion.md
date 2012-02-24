# Add mcrypt to PHP in OS X Lion (10.7.x)

I had found a great write up on how to do this [here](http://goo.gl/ybVTW), unfortunately Apple made changes to Xcode.  So if you have Xcode 4.3 or greater this write up will get you there.

### Assumptions

* Mac OS X Lion 10.7 or greater
* PHP 5.3.8
* Xcode 4.3 or greater
* curl CLI utilitiy

### Steps Overview

1.  Install libmcrypt-2.5.8
2.  Install autoconf-2.68
3.  Install PHP 5.3.8 source
4.  Turn on mcrypt in PHP

### Preliminary Setup

** Note:  All steps are performed from the command line. Also I would have preferred to install autoconf with HomeBrew, but there is some back and forth on it in the HomeBrew repo on github, so at some point it probably won't be necessary to do manually.

To make things easy and keep things tidy, create a directory to store all of the downloaded source code to:

$ cd ~; mkdir SourceCode

### Step 1: Install libmcrypt-2.5.8

1. Download libmcrypt-2.5.8

    $ curl -O http://superb-sea2.dl.sourceforge.net/project/mcrypt/Libmcrypt/2.5.8/libmcrypt-2.5.8.tar.gz
    
    $ tar xzf libmcrypt-2.5.8.tar.gz
    
    $ rm libmcrypt-2.5.8.tar.gz
    
    $ cd libmcrypt-2.5.8

2. Configure libmcrypt for compilation

    $ MACOSX_DEPLOYMENT_TARGET=10.7 CFLAGS='-O3 -fno-common -arch i386 -arch x86_64' LDFLAGS='-O3 -arch i386 -arch x86_64' CXXFLAGS='-O3 -fno-common -arch i386 -arch x86_64' ./configure --disable-dependency-tracking
    
3. Compile libmcrypt

    $ make -j6

4. Install the library

    $ sudo make install

### Step 2:  Install autoconf-2.68

1. Download autoconf-2.68
    
    Make sure we are in ~/SourceCode
    
    $ cd ~/SourceCode
    
    $ curl -O http://ftp.gnu.org/gnu/autoconf/autoconf-2.68.tar.gz
    
    $ tar xzf autoconf-2.68.tar.gz
    
    $ cd autoconf-2.68

2. Configure autoconf for compilation
    
    $ ./configure
    
3.  Compile autoconf

    $ make

4.  Install autoconf
    
    $ sudo make install

### Step 3:  Install PHP 5.3.8 source

1. Download PHP 5.3.8

    Make sure we are in ~/SourceCode
    
    $ cd ~/SourceCode
    
    $ curl -O http://www.php.net/distributions/php-5.3.8.tar.gz
    
    $ tar xzf php-5.3.8.tar.gz
    
2.  Prepare mcrypt extension for compiling

    $ cd php-5.3.8/ext/mcrypt
    
    $ /usr/bin/phpize
    
    $ MACOSX_DEPLOYMENT_TARGET=10.7 CFLAGS='-O3 -fno-common -arch i386 -arch x86_64' LDFLAGS='-O3 -arch i386 -arch x86_64' CXXFLAGS='-O3 -fno-common -arch i386 -arch x86_64' ./configure --with-php-config=/Application/Xcode.app/content/Developer/SDKs/MacOSX10.7.sdk/usr/bin/php-config
    
3.  Compile mcrypt.so

    $ make -j6
    
4.  Install mcrypt.so

    $ sudo make install

### Step 4:  Turn on mcrypt in PHP

If you have never made changes to your php.ini, then odds are you will have to create one from the php.ini.default, if not modify your current one.

    $ sudo cp /etc/php.ini.default /etc/php.ini
    
1.  Turn on dynamic loading of PHP Extensions (use your favorite editor, I use vim).  Change enable_dl = Off to enable_dl = On and remove the ; in front of it if it exists.

2.  Some where under the "Dynamic Extensions" header add the following line:  extension=mcrypt.so

Finally you need to restart apache to make sure that everything is working:  $ sudo apachectl -k restart

To check it, use [phpinfo()](http://goo.gl/W7zca);