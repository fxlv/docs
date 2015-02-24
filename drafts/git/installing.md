The Git version that comes with Wheezy is quite old so in certain situations
it makes sense to build the latest version from source.

I usually keep custom built software in my ~/opt

    git clone git clone git@github.com:git/git.git
    cd git
    apt-get install build-essential autoconf zlib1g-dev
    ./configure --prefix=${HOME}/opt --without-tcltk
    make -j4
    make install
