# #175 - How to use cmake or link an example? [Closed]

> Username: marbx  
> Created at: 2024-01-01 18:58:11 UTC  
> Updated at: 2024-01-02 11:01:39 UTC  
> Closed at: 2024-01-01 22:48:22 UTC  
> Url: https://github.com/boostorg/redis/issues/175  

Hello,  
because the Debian 12 Boost package `libboost-all-dev` does not include Redis, I "bootstrapped" boost 1.84.0 with  
```  
mkdir -p /home/marbx/boost/install  
./bootstrap.sh --prefix=/home/marbx/boost/install  
./b2 install  
```  
  
I can compile and run a Boost coroutine example using   
```  
g++ -Wl,--verbose=99 -std=c++20  example.cpp\  
    -I/home/marbx/boost/install/include\  
    -L/home/marbx/boost/install/lib\  
    -lboost_coroutine -lboost_context -lboost_system -lm -lxml2 -lssl -lcrypto  
  
export LD_LIBRARY_PATH=/home/marbx/boost/install/lib  
  
./a.out  
  
```  
  
  
  
When I try to run cmake, as described in the README,...  
```  
BOOST_ROOT=/home/marbx/boost/install    cmake --preset g++-11  
```  
  
... I get many errors:  
  
```  
CMake Error at CMakeLists.txt:83 (add_subdirectory):  
  add_subdirectory given source "../system" which is not an existing  
  directory.  
CMake Error at CMakeLists.txt:83 (add_subdirectory):  
  add_subdirectory given source "../assert" which is not an existing  
  directory.  
...snip...  
-- Could NOT find Protobuf (missing: Protobuf_LIBRARIES Protobuf_INCLUDE_DIR)  
-- Configuring incomplete, errors occurred!  
  
```  
  
  
When I try to compile the Redis `cpp17_intro` example using...  
```  
g++ -Wl,--verbose=99 -std=c++17 example/cpp17_intro.cpp\  
    -I/home/marbx/boost/install/include\  
    -L/home/marbx/boost/install/lib\  
    -lboost_thread -lssl -lcrypto -lm -lxml2 -lboost_coroutine -lboost_context -lboost_system  
```  
  
... I get many linker errors:  
```  
undefined reference to `boost::redis::connection::connection(boost::asio::io_context&, boost::asio::ssl::context_base::method, unsigned long)'  
undefined reference to `boost::redis::connection::cancel(boost::redis::operation)'  
...snip...  
undefined reference to `boost::redis::resp3::boost_redis_to_bulk  
collect2: error: ld returned 1 exit status  
```  
  
It seems I must specify a library, but there is no library called \*redis\* under `/home/marbx/boost/install/lib/`  
  
What am I missing?  
Thanks  
  
P.S.  
I corrected the g++ calls because they formerly used libraries from /usr/lib/*

---

## Comment 1

> Username: marbx  
> Created at: 2024-01-01 22:23:41 UTC  
> Url: https://github.com/boostorg/redis/issues/175#issuecomment-1873504705  

cancel is defined in connection.hpp, a header only file. There is no need for a library.  
The problem is that connection.hpp is not read, so the linker must assume it is in a library.  
  
`g++ -H`  shows that compiler reads 5 redis related files, namely:  
```  
/home/marbx/boost/install/include/boost/redis/logger.hpp  
/home/marbx/boost/install/include/boost/redis/detail/connector.hpp  
/home/marbx/boost/install/include/boost/redis/detail/resolver.hpp  
/home/marbx/boost/install/include/boost/redis/detail/handshaker.hpp  
/home/marbx/boost/install/include/boost/redis/usage.hpp  
```  
  
So the question is why the compiler does not read  
```  
/home/marbx/boost/install/include/boost/redis/connection.hpp   
```  
  
File permissions are regular

---

## Comment 2

> Username: mzimbres  
> Created at: 2024-01-01 22:28:13 UTC  
> Url: https://github.com/boostorg/redis/issues/175#issuecomment-1873505988  

Hi, there are some problems building with cmake in the master branch. I have made some fixes in the develop though. This should work  
  
```  
# Clone the superproject (perhaps using shallow copies)  
$ git clone --recurse-submodules https://github.com/boostorg/boost  
  
# Checkout the develop branch  
$ git submodule foreach --recursive git checkout develop  
  
# Do an out of source build  
$ mkir build  
$ cmake ../boostorg/libs/redis  
$ cmake --build . --target cpp20_intro  
```  
Let me know if this works for you.

---

## Comment 3

> Username: mzimbres  
> Created at: 2024-01-01 22:31:55 UTC  
> Url: https://github.com/boostorg/redis/issues/175#issuecomment-1873507025  

>cancel is defined in connection.hpp, a header only file. There is no need for a library.  
> The problem is that connection.hpp is not read, so the linker must assume it is in a library.  
  
You need to build the library yourself by including `#include <boost/redis/src.hpp>` in one cpp file, it can be the one where you define the main function.

---

## Comment 4

> Username: marbx  
> Created at: 2024-01-01 22:48:22 UTC  
> Url: https://github.com/boostorg/redis/issues/175#issuecomment-1873511967  

Adding `#include <boost/redis/src.hpp>` to example/cpp17_intro.cpp results in 0 errors and  a nice and cozy `PING: Hello world` :-)  
  
Excellent!  
  
Thank you a lot. Looking forward to continue from here, and to look into cloning the superproject, too.  
  
Closing the issue, because the README already contains the advise. I just overlooked it.

---

## Comment 5

> Username: marbx  
> Created at: 2024-01-02 11:01:38 UTC  
> Url: https://github.com/boostorg/redis/issues/175#issuecomment-1873883276  

> Hi, there are some problems building with cmake in the master branch. I have made some fixes in the develop though. This should work  
>   
> ```  
> # Clone the superproject (perhaps using shallow copies)  
> $ git clone --recurse-submodules https://github.com/boostorg/boost  
>   
> # Checkout the develop branch  
> $ git submodule foreach --recursive git checkout develop  
>   
> # Do an out of source build  
> $ mkir build  
> $ cmake ../boostorg/libs/redis  
> $ cmake --build . --target cpp20_intro  
> ```  
>   
> Let me know if this works for you.  
  
@mzimbres , yes, this worked out fine and I executed  
```  
$ example/cpp20_intro   
PING: Hello world  
```  
I like this out-of-source-build path, I now only have to better understand cmake :-)
