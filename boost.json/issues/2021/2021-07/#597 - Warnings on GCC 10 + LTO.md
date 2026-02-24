# #597 - Warnings on GCC 10 + LTO [Closed]

> Username: phprus  
> Created at: 2021-07-27 10:54:21 UTC  
> Updated at: 2024-10-09 15:47:50 UTC  
> Closed at: 2024-10-09 15:47:49 UTC  
> Url: https://github.com/boostorg/json/issues/597  

Commit: 26cedfabfc1ff13cd6fbc3d8ae03c33724d2343d  
  
GCC: ``gcc version 10.2.1 20200825 [revision c0746a1beb1ba073c7981eb09f55b3d993b32e5c] (SUSE Linux)``  
  
CMake command:  
```  
CC=gcc-10 CXX=g++-10 cmake -DCMAKE_VERBOSE_MAKEFILE=ON -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_STANDARD=17 -DBOOST_JSON_STANDALONE=ON -DBUILD_TESTING=ON -DBOOST_JSON_BUILD_TESTS=ON -DBOOST_JSON_BUILD_FUZZERS=OFF -DBOOST_JSON_BUILD_EXAMPLES=OFF -DCMAKE_CXX_FLAGS="-flto -fno-fat-lto-objects" ../..  
```  
  
Warnings:  
```  
[ 12%] Linking CXX executable boost_json-limits  
cd /home/.../boostorg/json/json-26cedfabfc1ff13cd6fbc3d8ae03c33724d2343d/build/gcc10/test && /usr/bin/cmake -E cmake_link_script CMakeFiles/boost_json-limits.dir/link.txt --verbose=1  
/usr/bin/g++-10  -flto -fno-fat-lto-objects -O3 -DNDEBUG   CMakeFiles/boost_json-limits.dir/limits.cpp.o CMakeFiles/boost_json-limits.dir/main.cpp.o CMakeFiles/boost_json-limits.dir/__/src/src.cpp.o  -o boost_json-limits   
In function ‘relocate’,  
    inlined from ‘__ct ’ at /home/.../boostorg/json/json-26cedfabfc1ff13cd6fbc3d8ae03c33724d2343d/include/boost/json/impl/array.ipp:92:17,  
    inlined from ‘insert’ at /home/.../boostorg/json/json-26cedfabfc1ff13cd6fbc3d8ae03c33724d2343d/include/boost/json/impl/array.ipp:458:26,  
    inlined from ‘testArray.constprop’ at /home/.../boostorg/json/json-26cedfabfc1ff13cd6fbc3d8ae03c33724d2343d/test/limits.cpp:169:13:  
/home/.../boostorg/json/json-26cedfabfc1ff13cd6fbc3d8ae03c33724d2343d/include/boost/json/impl/array.hpp:122:17: warning: ‘__builtin_memmove’ writing between 24 and 103079215080 bytes into a region of size 0 overflows the destination [-Wstringop-overflow=]  
  122 |     std::memmove(  
      |                 ^  
In member function ‘term’,  
    inlined from ‘resize.constprop’ at /home/.../boostorg/json/json-26cedfabfc1ff13cd6fbc3d8ae03c33724d2343d/include/boost/json/impl/string.ipp:357:19:  
/home/.../boostorg/json/json-26cedfabfc1ff13cd6fbc3d8ae03c33724d2343d/include/boost/json/detail/string_impl.hpp:313:23: warning: writing 1 byte into a region of size 0 [-Wstringop-overflow=]  
  313 |             s_.buf[n] = 0;  
      |                       ^  
/home/.../boostorg/json/json-26cedfabfc1ff13cd6fbc3d8ae03c33724d2343d/include/boost/json/impl/string.ipp: In member function ‘resize.constprop’:  
/home/.../boostorg/json/json-26cedfabfc1ff13cd6fbc3d8ae03c33724d2343d/include/boost/json/detail/string_impl.hpp:62:14: note: at offset [100, 1001] to object ‘buf’ with size 15 declared here  
   62 |         char buf[sbo_chars_ + 1];  
      |              ^  
In member function ‘term’,  
    inlined from ‘append’ at /home/.../boostorg/json/json-26cedfabfc1ff13cd6fbc3d8ae03c33724d2343d/include/boost/json/detail/impl/string_impl.ipp:172:13,  
    inlined from ‘append’ at /home/.../boostorg/json/json-26cedfabfc1ff13cd6fbc3d8ae03c33724d2343d/include/boost/json/impl/string.ipp:239:35,  
    inlined from ‘testString.constprop’ at /home/.../boostorg/json/json-26cedfabfc1ff13cd6fbc3d8ae03c33724d2343d/test/limits.cpp:192:17:  
/home/.../boostorg/json/json-26cedfabfc1ff13cd6fbc3d8ae03c33724d2343d/include/boost/json/detail/string_impl.hpp:313:23: warning: writing 1 byte into a region of size 0 [-Wstringop-overflow=]  
  313 |             s_.buf[n] = 0;  
      |                       ^  
/home/.../boostorg/json/json-26cedfabfc1ff13cd6fbc3d8ae03c33724d2343d/test/limits.cpp: In function ‘testString.constprop’:  
/home/.../boostorg/json/json-26cedfabfc1ff13cd6fbc3d8ae03c33724d2343d/include/boost/json/detail/string_impl.hpp:62:14: note: at offset [886, 4294968294] to object ‘buf’ with size 15 declared here  
   62 |         char buf[sbo_chars_ + 1];  
      |              ^  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-07-27 13:16:56 UTC  
> Url: https://github.com/boostorg/json/issues/597#issuecomment-887504003  

I've investigated the errors and as far as I can see, the static analyzer GCC uses when lto is enabled is kind of faulty. If the library is built in debug mode gcc do see stuff better and the warning doesn't appear. I recommend you to disable the warning (at least, when building Json).

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-07-27 19:13:39 UTC  
> Url: https://github.com/boostorg/json/issues/597#issuecomment-887766762  

Is this an shipping release of GCC or is it a beta?

---

## Comment 3

> Username: phprus  
> Created at: 2021-07-27 19:28:09 UTC  
> Url: https://github.com/boostorg/json/issues/597#issuecomment-887775732  

@vinniefalco   
This is GCC from openSUSE 15.2.  
  
@grisumbras   
Maybe disable this warning for the whole json library?  
  
Like https://github.com/chriskohlhoff/asio/blob/master/asio/include/asio/detail/push_options.hpp and https://github.com/chriskohlhoff/asio/blob/master/asio/include/asio/detail/pop_options.hpp in ASIO?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-07-27 20:04:06 UTC  
> Url: https://github.com/boostorg/json/issues/597#issuecomment-887797700  

Do you think that's best?

---

## Comment 5

> Username: grisumbras  
> Created at: 2021-07-29 07:07:35 UTC  
> Url: https://github.com/boostorg/json/issues/597#issuecomment-888859191  

I'm not sure it's actually doable. The warning is emitted by the linker and it seems LTO "objects" don't store pragma information, so disabling this warning with a pragma doesn't do anything. The only way I've managed to disable the warning is to pass "-Wno-stringop-overflow" to the linker. I guess, we could add linking flags to tests via build scripts.

---

## Comment 6

> Username: phprus  
> Created at: 2021-08-03 07:11:53 UTC  
> Url: https://github.com/boostorg/json/issues/597#issuecomment-891596053  

@grisumbras, you're right, pragma doesn't work. Linker flag required.

---

## Comment 7

> Username: grisumbras  
> Created at: 2024-10-09 15:47:41 UTC  
> Url: https://github.com/boostorg/json/issues/597#issuecomment-2402700848  

I'm inclined to do nothing here.
