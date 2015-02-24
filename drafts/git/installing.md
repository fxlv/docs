The Git version that comes with Wheezy is quite old so in certain situations
it makes sense to build the latest version from source.

I usually keep custom built software in my ~/opt

    apt-get install build-essential autoconf zlib1g-dev
    ./configure --prefix=/home/fx/opt --without-tcltk
    make -j4
    make install
