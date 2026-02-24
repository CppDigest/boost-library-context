# #198 - Problem with installation [Closed]

> Username: SergeyPod  
> Created at: 2024-04-25 11:41:21 UTC  
> Updated at: 2024-04-26 01:54:53 UTC  
> Closed at: 2024-04-26 01:54:53 UTC  
> Url: https://github.com/boostorg/redis/issues/198  

Hi, I tried to install Boost.Redis in two ways, both failed. The fist approach, as suggested here (*):  
  
```  
(base) <username>@<machinename>:~/tmp/redis$ BOOST_ROOT=/usr/local cmake --preset g++-11  
Preset CMake variables:  
  
  CMAKE_BUILD_TYPE="Debug"  
  CMAKE_CXX_COMPILER="g++-11"  
  CMAKE_CXX_EXTENSIONS="OFF"  
  CMAKE_CXX_FLAGS="-Wall -Wextra -fsanitize=address"  
  CMAKE_CXX_STANDARD_REQUIRED="ON"  
  CMAKE_SHARED_LINKER_FLAGS="-fsanitize=address"  
  PROJECT_BINARY_DIR="/home/<username>/tmp/redis/build/g++-11"  
  
CMake Error at CMakeLists.txt:84 (add_subdirectory):  
  add_subdirectory given source "../system" which is not an existing  
  directory.  
  
  
CMake Error at CMakeLists.txt:84 (add_subdirectory):  
  add_subdirectory given source "../assert" which is not an existing  
  directory.  
....  
  
CMake Error at CMakeLists.txt:84 (add_subdirectory):  
  add_subdirectory given source "../json" which is not an existing directory.  
  
  
CMake Error at CMakeLists.txt:84 (add_subdirectory):  
  add_subdirectory given source "../endian" which is not an existing  
  directory.  
  
  
-- Found Protobuf: /usr/lib/x86_64-linux-gnu/libprotobuf.so (found version "3.12.4")  
-- Configuring incomplete, errors occurred!  
  
```  
  
The second is compilation of entire boost library. It looks like redis is not in the list of boost modules:  
  
```  
(base) <username>@<machinename>:~/tmp/boost_1_85_0$ ./b2 --show-libraries  
The following libraries require building:  
    - atomic  
    - charconv  
    - chrono  
    - cobalt  
    - container  
    - context  
    - contract  
    - coroutine  
    - date_time  
    - exception  
    - fiber  
    - filesystem  
    - graph  
    - graph_parallel  
    - headers  
    - iostreams  
    - json  
    - locale  
    - log  
    - math  
    - mpi  
    - nowide  
    - program_options  
    - python  
    - random  
    - regex  
    - serialization  
    - stacktrace  
    - system  
    - test  
    - thread  
    - timer  
    - type_erasure  
    - url  
    - wave  
  
```  
Can you help with this?  
Thanks a lot in advance.  
  
(*) https://www.boost.org/doc/libs/1_85_0/libs/redis/doc/html/index.html

---

## Comment 1

> Username: anarthal  
> Created at: 2024-04-25 18:42:51 UTC  
> Url: https://github.com/boostorg/redis/issues/198#issuecomment-2077942655  

This is a header-only library, so you don't actually need to build it with b2.  
  
I'd advise to:  
- Install Boost headers (fastest would be `b2 --with-system --prefix=path/to/boost d0 install`)  
- Consume it via cmake, by doing `find_package(Boost REQUIRED COMPONENTS headers`), then link to `Boost::headers` in your cmake. Remember to add the Boost path to `CMAKE_PREFIX_PATH` so cmake finds it.  
  
IIRC (Marcelo can probably tell you better), you also need to include the file `boost/redis/src.hpp` exactly in one of your .cpp files.  
  
Hope it helps.

---

## Comment 2

> Username: anarthal  
> Created at: 2024-04-25 18:44:04 UTC  
> Url: https://github.com/boostorg/redis/issues/198#issuecomment-2077944656  

The instructions to consume the library seem outdated, from the time before Redis was included in Boost. We should update them.

---

## Comment 3

> Username: SergeyPod  
> Created at: 2024-04-26 01:54:53 UTC  
> Url: https://github.com/boostorg/redis/issues/198#issuecomment-2078491118  

Thank you, @anarthal , provided suggestion worked for me.
