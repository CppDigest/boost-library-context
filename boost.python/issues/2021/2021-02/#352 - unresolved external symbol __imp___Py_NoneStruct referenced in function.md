# #352 - unresolved external symbol __imp___Py_NoneStruct referenced in function [Closed]

> Username: KOOKOKOK  
> Created at: 2021-02-18 15:23:09 UTC  
> Updated at: 2021-02-19 01:56:26 UTC  
> Closed at: 2021-02-19 01:56:26 UTC  
> Url: https://github.com/boostorg/python/issues/352  

python 3.7  
./bootstrap.sh  
 ./b2  
 ./b2 install

---

## Comment 1

> Username: KOOKOKOK  
> Created at: 2021-02-18 15:39:00 UTC  
> Url: https://github.com/boostorg/python/issues/352#issuecomment-781431799  

(beta) pan@pan-System-Product-Name:~/anaconda3/envs/beta/lib$ nm libpython3.7m.so  | grep Py_Non  
00000000003467f0 D _Py_NoneStruct  
00000000000cb0f0 T _Py_Nonlocal  
  
 It's strange ？？？  
  
unresolved external symbol __imp___Py_NoneStruct referenced in function

---

## Comment 2

> Username: KOOKOKOK  
> Created at: 2021-02-18 15:39:25 UTC  
> Url: https://github.com/boostorg/python/issues/352#issuecomment-781432100  

cmake_minimum_required(VERSION 3.10)  
  
project(py2c)  
find_package(Boost REQUIRED COMPONENTS python37)  
include_directories(/home/pan/anaconda3/envs/beta/include/python3.7m)  
add_executable(py2c main.cpp)  
link_directories(  
        /home/pan/anaconda3/envs/beta/lib/libpython3.7m.so   
)  
target_link_libraries( py2c Boost::python37 )

---

## Comment 3

> Username: KOOKOKOK  
> Created at: 2021-02-18 15:39:56 UTC  
> Url: https://github.com/boostorg/python/issues/352#issuecomment-781432470  

// #define BOOST_BIND_GLOBAL_PLACEHOLDERS  
#include<boost/python.hpp>  
#include<iostream>  
  
int main(){  
  
        return 0;  
}  
  
  
[proc] Executing command: /usr/local/bin/cmake --build /home/pan/Cpro/learning/py2c/build --config Release --target all -- -j 18  
[build] [ 50%] Linking CXX executable py2c  
[build] CMakeFiles/py2c.dir/main.cpp.o：在函数‘_GLOBAL__sub_I_main’中：  
[build] main.cpp:(.text.startup+0x21)：对‘_Py_NoneStruct’未定义的引用  
[build] main.cpp:(.text.startup+0x33)：对‘_Py_NoneStruct’未定义的引用  
[build] collect2: error: ld returned 1 exit status  
[build] CMakeFiles/py2c.dir/build.make:103: recipe for target 'py2c' failed  
[build] make[2]: *** [py2c] Error 1  
[build] make[1]: *** [CMakeFiles/py2c.dir/all] Error 2  
[build] CMakeFiles/Makefile2:94: recipe for target 'CMakeFiles/py2c.dir/all' failed  
[build] Makefile:102: recipe for target 'all' failed  
[build] make: *** [all] Error 2  
[build] Build finished with exit code 2

---

## Comment 4

> Username: KOOKOKOK  
> Created at: 2021-02-19 01:56:12 UTC  
> Url: https://github.com/boostorg/python/issues/352#issuecomment-781759813  

cmake_minimum_required(VERSION 3.10)  
  
project(py2c)  
find_package(Boost REQUIRED  )  
include_directories(/home/pan/anaconda3/envs/beta/include)  
include_directories(/home/pan/anaconda3/envs/beta/include/python3.7m)  
link_libraries(  
        /home/pan/anaconda3/envs/beta/lib/libpython3.7m.so   
)  
add_executable(py2c main.cpp)  
  
target_link_libraries( py2c ${Boost_LIBRARIES} )  
  
解决了 link_directories -》link_libraries 且在 add_executable之前 使用anaconda在虚拟化境下就可以无需加额外参数。  
但在  .b2之前要配置一下    using python : 3.7 : /home/pan/anaconda3/envs/beta/ : /home/pan/anaconda3/envs/beta/include/python3.7m : /home/pan/anaconda3/envs/beta/lib ;  在project-config.jam里(boost文件夹中)
