# #2113 - MacOS: finds Boost but refers to non-existing Boost files [Closed]

> Username: mouse07410  
> Created at: 2020-10-25 23:55:15 UTC  
> Updated at: 2020-10-26 15:19:50 UTC  
> Closed at: 2020-10-26 15:19:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2113  

### Version of Beast  
  
```  
$ git log -1  
commit 855fc23885307aeb6ad1318001a4bbdd4345d47c (HEAD -> develop, origin/develop, origin/HEAD)  
Author: Richard Hodges <hodges.r@gmail.com>  
Date:   Thu Sep 3 16:11:38 2020 +0200  
  
    Set version to 302  
```  
  
### Steps necessary to reproduce the problem  
  
```sh  
$ git clone https://github.com/boostorg/beast.git  
$ cd beast && mkdir build && cd build  
$ cmake -DCMAKE_INSTALL_PREFIX=/opt/local ..  
-- The C compiler identification is AppleClang 12.0.0.12000032  
-- The CXX compiler identification is AppleClang 12.0.0.12000032  
-- Detecting C compiler ABI info  
-- Detecting C compiler ABI info - done  
-- Check for working C compiler: /opt/local/bin/clang - skipped  
-- Detecting C compile features  
-- Detecting C compile features - done  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: /opt/local/bin/clang++ - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Looking for pthread.h  
-- Looking for pthread.h - found  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success  
-- Found Threads: TRUE    
-- Found OpenSSL: /opt/local/lib/libcrypto.dylib (found version "1.1.1h")    
-- Found Boost: /opt/local/include (found version "1.71.0") found components: coroutine filesystem system context   
-- Configuring done  
-- Generating done  
-- Build files have been written to: /Users/ur20980/src/beast/build  
$ $ make 2>&1 | tee make-out.txt  
Scanning dependencies of target lib-asio  
[  0%] Building CXX object CMakeFiles/lib-asio.dir/test/lib_asio.cpp.o  
[  1%] Linking CXX static library liblib-asio.a  
[  1%] Built target lib-asio  
Scanning dependencies of target lib-beast  
[  2%] Building CXX object CMakeFiles/lib-beast.dir/test/lib_beast.cpp.o  
In file included from /Users/ur20980/src/beast/test/lib_beast.cpp:12:  
In file included from /Users/ur20980/src/beast/include/boost/beast/src.hpp:30:  
In file included from /Users/ur20980/src/beast/include/boost/beast/_experimental/test/impl/stream.ipp:13:  
In file included from /Users/ur20980/src/beast/include/boost/beast/_experimental/test/stream.hpp:14:  
In file included from /Users/ur20980/src/beast/include/boost/beast/core/bind_handler.hpp:14:  
/Users/ur20980/src/beast/include/boost/beast/core/detail/bind_handler.hpp:158:18: error: no type named 'asio_handler_invoke_is_deprecated' in namespace 'boost::asio'  
    boost::asio::asio_handler_invoke_is_deprecated  
    ~~~~~~~~~~~~~^  
.  .  .  .  .  
In file included from /Users/ur20980/src/beast/test/lib_beast.cpp:12:  
In file included from /Users/ur20980/src/beast/include/boost/beast/src.hpp:30:  
In file included from /Users/ur20980/src/beast/include/boost/beast/_experimental/test/impl/stream.ipp:13:  
In file included from /Users/ur20980/src/beast/include/boost/beast/_experimental/test/stream.hpp:603:  
/Users/ur20980/src/beast/include/boost/beast/_experimental/test/impl/stream.hpp:17:10: fatal error: 'boost/asio/any_io_executor.hpp' file not found  
#include <boost/asio/any_io_executor.hpp>  
         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
15 errors generated.  
make[2]: *** [CMakeFiles/lib-beast.dir/build.make:82: CMakeFiles/lib-beast.dir/test/lib_beast.cpp.o] Error 1  
make[1]: *** [CMakeFiles/Makefile2:1410: CMakeFiles/lib-beast.dir/all] Error 2  
$  
$ find /opt/local/include/boost -name any_io_executor.hpp -print  
$   
```  
  
And the complete `make-out.txt`: [make-out.txt](https://github.com/boostorg/beast/files/5436152/make-out.txt)  
  
### All relevant compiler information  
  
macOS Catalina 10.15.7, Xcode-12.1  
  
```  
$ sw_vers  
ProductName:	Mac OS X  
ProductVersion:	10.15.7  
BuildVersion:	19H2  
$ clang++ --version  
Apple clang version 12.0.0 (clang-1200.0.32.21)  
Target: x86_64-apple-darwin19.6.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-10-26 06:05:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2113#issuecomment-716325625  

Latest beast requires latest boost.asio

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-10-26 12:29:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2113#issuecomment-716514969  

Hi there,   
  
I've just checked this on my mac. Beast has to keep up with changes in Asio on which it depends. I notice that you a re building beast against boost version 1.71. If you're building 1.74 version of beast or later this won't work as Asio's interface changed in Boost version 1.74.  
  
You have a few optioins:  
  
1. use the version of Beast bundled with boost 1.71  
2. install boost 1.74 and try again  
  
Please let me know how you get on.

---

## Comment 3

> Username: mouse07410  
> Created at: 2020-10-26 15:19:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2113#issuecomment-716617808  

I did not realize that Beast is included in the Boost distro - otherwise I wouldn't even tried to (re-)build it from the source.  
  
Getting examples from Boost 1.71 (somewhat of a pain, because it seems that I have to download each individual file), they appear to work on my Boost 1.71 installation.  
  
Thank you!  
  
P.S. I probably missed it - but if not, you may want to add a statement to the README that it's unnecessary to build Beast from sources, as Boost already includes it.
