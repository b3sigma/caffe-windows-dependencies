# caffe-windows-dependencies
This project sets up a super build of most of the [Caffe](https://github.com/BVLC/caffe) library dependencies on Windows. The dependencies built by this project are:
* gflags
* glog
* leveldb
* lmdb
* protobuf
* snappy
* opencv
* hdf5

It does not build the following dependencies:
* boost


Windows binaries for these dependencies are widely available. Please note that boost is required to build the windows version of leveldb.

The project is setup via git submodules pointing to either the original code repository on github or a fork of the project so that a CMake-based build is available.

When this project is built it will generate binaries and includes (via CMake install) and a cmake cache file that cane be used so configure the CMake based build of Caffe. Here is what an initial setup would look like

	git clone https://github.com/b3sigma/caffe.git caffe
	cd caffe
	git checkout windows
	git clone --recursive https://github.com/b3sigma/caffe-windows-dependencies.git dep
	md dependencies
	cd 	dependencies\
	cmake ..\dep -G "<Your compiler> Win64" -DBOOST_ROOT=/path/to/boost/ -DBoost_USE_STATIC_LIBS=ON
	# Specific example I used:
	# cmake ..\dep -G "Visual Studio 12 2013 Win64" -DBOOST_ROOT=/boost/boost_1_56_0/ -DBoost_USE_STATIC_LIBS=ON -DBOOST_LIBRARYDIR=/boost/boost_1_56_0/lib64-msvc-12.0/
	cmake --build . --config Release
	cd ..
	md build
	cd build
	cmake -G "<Your compiler> Win64" -C ../dependencies/install/caffe-cache-init.cmake ..
	# Specific example I used:
	# cmake -G "Visual Studio 12 2013 Win64" -C ../dependencies/install/caffe-cache-init.cmake ..
	cmake --build . --config Release


### Problems encountered 
* Boost directories weren't listed as being found, despite specifying the root
    * fix: added -DBOOST_LIBRARYDIR=/path/to/boost/libs/
* had a protoc.exe in my path, cmake picked it up and used it to generate protocol buffers, but then compilations failed because versions didn't match
    * fix: removed protoc.exe from my path, as I didn't really need it there
* Initially didn't specific a generator, using default. Got build errors resolving around casts from pointer to dword.
    * fix: specified generator explicitly, including Win64 in the definition
    * In retrospect, this probably caused the BOOST_LIBRARYDIR issue.
* linker errors, where google log doesn't seem to be matching up correctly
    * fix: The problem was a define that altered glog function signatures to be static that was only defined when cmake built the project directly. When the caffe.lib was build, it included glog/logging.h, but because the define wasn't present anymore, the function signatures were in dll mode. The mismatch between a static built lib and dll includes meant the linker couldn't match up signatures. The fix was to modify signatures to default to static for windows builds. Not a great fix, more of a hack that got things going, but the CMakeLists.txt is really structred around building static windows libs anyway so it works.
    
This was only test with Visual Studio 2013 64 bit build and CMake 3.2.