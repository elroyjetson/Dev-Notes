# Fix CentOS 6.2 libyaml problem

First install the libyaml

    wget http://pyyaml.org/download/libyaml/yaml-0.1.4.tar.gz
    tar xzvf yaml-0.1.4.tar.gz; rm yaml-0.1.4.tar.gz
    cd yaml-0.1.4/
    ./configure --prefix=/usr/local
    make
    sudo make install

Reinstall latest version

    wget http://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.3-p194.tar.gz
    tar xzvf ruby-1.9.3-p194.tar.gz; rm ruby-1.9.3-p194.tar.gz
    cd ruby-1.9.3-p194/
    ./configure --prefix=/usr/local --enable-shared --disable-install-doc --with-opt-dir=/usr/local/lib
    make
    sudo make install