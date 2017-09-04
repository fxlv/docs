# Heaptrack

Heaptrack is a wonderul tool for finding memory leaks in C/C++ applications.
It can be used as a CLI tool on the servers and afterwards it has a nice GUI to analyze the data on your laptop.

Sounds good, right, well, it is, but as with everything, there is a catch. Building it, takes a bit of patience.


## Building the cli heaptrack

This is the easy part, simply get the dependencies, clone the repo and build it.

Get the dependencies
```
apt-get install cmake libdwarf-dev libunwind-dev libboost-dev libboost-iostreams-dev libboost-program-options-dev zlib1g-dev
```

Build it
```
git clone https://github.com/KDE/heaptrack.git
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make
```

really as simple as that, you now have `./bin/heaptrack`

## Building heaptrack_gui

This is where the somewhat longer process is required.
Essentially heaptrack depends on kchart which is part of kdiagram, but kdiagram needs latest cmake extra modules.

So we need to build cmake extra modules, kdiagram and then we can build heaptrack with GUI support.

Let's get going.

Get the dependencies
```
apt-get install extra-cmake-modules qtbase5-dev kio-dev libkf5coreaddons-dev libkf5threadweaver-dev libkf5itemmodels-dev libkf5configwidgets-dev gettext libsparsehash-dev qttools5-dev-tools qtdeclarative5-dev
```

Now get the source for extra cmake modules and build it (this is straightforward):

```
git clone https://github.com/KDE/extra-cmake-modules.git
mkdir build 
cd build
cmake ..
make
sudo make install
```

So far so good, eh? Ok then.

Now, if we are to see the pretty graphs that heaptrack_gui can draw we need to have kchart, which is part of kdiagram package. Sadly this is not provided by Ubuntu at all, so we must fetch source.

Then install kdiagram from source: 

First, install dependencies:

```
apt-get install libqt5svg5-dev
```

get the kdiagram source and try to build it

```
git clone git://anongit.kde.org/kdiagram
mkdir kdiagram/build
cd kdiagram/build
cmake ..
```

at this point you might get complaints from cmake about Qt being to old (requiring 5.6.0). I hacked this to 5.5.0 and it works ok. This is done in  `../CMakeLists.txt` on line `38` 

```
CMake Error at CMakeLists.txt:40 (find_package):
  Could not find a configuration file for package "Qt5" that is compatible
  with requested version "5.6.0".

  The following configuration files were considered but not accepted:

    /usr/lib/x86_64-linux-gnu/cmake/Qt5/Qt5Config.cmake, version: 5.5.1

```

At this point hopefully `cmake` finds everything it needs and we can proceed with the build.
This is much longer build than the others, so give it more jobs if you have extra cpu cores available

```
make -j 4 
sudo make install
```

Lastly, build heaptrack, this time with gui support.
Important step here is to check output of cmake to see if all the requirements were met.

```
git clone https://github.com/KDE/heaptrack.git
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make
```
