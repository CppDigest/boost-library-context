# #435 - Buffer overflow warning [Closed]

> Username: djarek  
> Created at: 2020-09-30 19:09:48 UTC  
> Updated at: 2020-10-05 00:04:49 UTC  
> Closed at: 2020-10-05 00:04:49 UTC  
> Url: https://github.com/boostorg/json/issues/435  

Compiling in Ubuntu 20.04 with the following cmake commandline:  
```  
cmake -DBOOST_JSON_STANDALONE=ON -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_FLAGS="-fvisibility=hidden -flto" ..  
```  
Produces the following warning when compiling tests:  
```  
[ 80%] Building CXX object test/CMakeFiles/tests.dir/static_resource.cpp.o  
In function ‘memmove’,  
    inlined from ‘relocate’ at /home/damian/json/test/../include/boost/json/impl/array.ipp:789:17,  
    inlined from ‘__ct ’ at /home/damian/json/test/../include/boost/json/impl/array.ipp:91:17,  
    inlined from ‘insert’ at /home/damian/json/test/../include/boost/json/impl/array.ipp:473:26,  
    inlined from ‘testArray.constprop’ at /home/damian/json/test/limits.cpp:132:13:  
/usr/include/x86_64-linux-gnu/bits/string_fortified.h:40:34: warning: ‘__builtin_memmove’ writing between 24 and 103079215080 bytes into a region of size 0 overflows the destination [-Wstringop-overflow=]  
   40 |   return __builtin___memmove_chk (__dest, __src, __len, __bos0 (__dest));  
````  
  
GCC version:  
```  
damian@DESKTOP-OE8CJIK:~/json/build$ g++ --version  
g++ (Ubuntu 9.3.0-10ubuntu2) 9.3.0  
Copyright (C) 2019 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```  
  
This may be a false-positive.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-30 20:13:04 UTC  
> Url: https://github.com/boostorg/json/issues/435#issuecomment-701619307  

I am inclined to believe it is a false positive. I stepped through it on both 32 and 64 bit builds.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-10-05 00:04:49 UTC  
> Url: https://github.com/boostorg/json/issues/435#issuecomment-703335726  

I can't do anything about this, the code is correct. If you think the code is incorrect, please provide an explanation or counterexample. I'm open to accepting a pull request that rewrites the code in question a different way (but achieves the same result) if doing so makes the warning go away.
