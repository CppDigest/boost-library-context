# #90 - wrap_python.hpp won't find necessary .h files [Closed]

> Username: ra0x3  
> Created at: 2016-10-19 02:52:50 UTC  
> Updated at: 2016-11-28 18:32:14 UTC  
> Closed at: 2016-11-28 18:32:14 UTC  
> Url: https://github.com/boostorg/python/issues/90  

I've installed boost on my Mac (running CLion on El Captain) and it installed with no problems. However now I'm trying to run a simple .cpp HelloWorld program that simply imports boost (without using it yet):  
  
``` c++  
#include <boost/python.hpp>  
#include <iostream>  
  
  
int main() {  
    std::cout << "Hello, World!" << std::endl;  
    return 0;  
    }  
```  
  
But when I run this I get the following error:  
  
```  
/Applications/CLion.app/Contents/bin/cmake/bin/cmake --build /Users/ralston/Library/Caches/CLion2016.2/cmake/generated/HelloWorld-ca434ad0/ca434ad0/Debug --target HelloWorld -- -j 4  
Scanning dependencies of target HelloWorld  
[ 50%] Building CXX object CMakeFiles/HelloWorld.dir/main.cpp.o  
In file included from /Users/ralston/Desktop/CLion/extensions/HelloWorld/main.cpp:1:  
In file included from /usr/local/include/boost/python.hpp:11:  
In file included from /usr/local/include/boost/python/args.hpp:8:  
In file included from /usr/local/include/boost/python/detail/prefix.hpp:13:  
/usr/local/include/boost/python/detail/wrap_python.hpp:50:11: fatal error: 'pyconfig.h' file not found  
# include <pyconfig.h>  
          ^  
1 error generated.  
make[3]: *** [CMakeFiles/HelloWorld.dir/main.cpp.o] Error 1  
make[2]: *** [CMakeFiles/HelloWorld.dir/all] Error 2  
make[1]: *** [CMakeFiles/HelloWorld.dir/rule] Error 2  
make: *** [HelloWorld] Error 2  
```  
  
I'm raising this as an issue because I've read another question similar to this, and I tried the recommended solutions from that issue (e.g., `$CPLUS_INCLUDE_PATH="$CPLUS_INCLUDE_PATH:/Library/Framework/Python.framework/Versions/3.5/Headers/[here are the necessary .h files]"` but that didn't work.  
  
The other issue I saw related to this involved someone compiling from the command line. Is there any way to get the `wrap_python.hpp` file to recognize the appropriate .h files?   
  
Here is my CMakeLists.txt file  
  
``` txt  
cmake_minimum_required(VERSION 3.6)  
project(HelloWorld)  
  
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")  
  
include_directories("/usr/local/include") # where root boost director sits  
  
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY, "HelloWorld")  
#set(CMAKE_VERBOSE_MAKEFILE ON)  
set(SOURCE_FILES main.cpp)  
add_executable(HelloWorld ${SOURCE_FILES})  
```

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2016-10-19 11:48:36 UTC  
> Url: https://github.com/boostorg/python/issues/90#issuecomment-254789898  

Just as the error message indicates: "pyconfig.h" isn't found. This is part of Python, not Boost, so make sure you have Python installed correctly, and add an include search path to find its header(s).
