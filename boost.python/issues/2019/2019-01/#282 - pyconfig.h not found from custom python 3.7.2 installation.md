# #282 - pyconfig.h not found from custom python 3.7.2 installation [Open]

> Username: ralhei  
> Created at: 2019-01-11 10:09:29 UTC  
> Updated at: 2022-04-12 07:16:15 UTC  
> Url: https://github.com/boostorg/python/issues/282  

When compiling boost against a custom python 3.7.2 installation the following error occurrs:  
```  
In file included from ./boost/python/detail/prefix.hpp:13:0,  
                 from ./boost/python/list.hpp:8,  
                 from libs/python/src/list.cpp:5:  
./boost/python/detail/wrap_python.hpp:50:23: fatal error: pyconfig.h: No such file or directory  
 # include <pyconfig.h>  
```  
This is due to the fact that the include files are located within a directory called `python3.7m` in the installation include directory:  
```  
$ ls -l /usr/local/Python-3.7.2/include  
drwxr-xr-x. 3 ralph users 4096 Jan 10 11:05 python3.7m  
```  
**Creating a softlink from `python3.7m` to `python3.7` solves the problem.**  
  
This problem happened on a Centos7 installation. Python was compiled and installed with pretty much standard compile options:  
```  
$ ./configure CFLAGS=-fPIC --prefix=/usr/local/Python-3.7.2 --enable-optimizations  
```

---

## Comment 1

> Username: jhdub23  
> Created at: 2019-11-07 00:26:39 UTC  
> Url: https://github.com/boostorg/python/issues/282#issuecomment-550563540  

This issue still exists with python-3.7.4 and boost-1_71_0.  Thanks for posting the workaround.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2019-12-17 19:28:30 UTC  
> Url: https://github.com/boostorg/python/issues/282#issuecomment-566712712  

Sorry, I'm not sure what this is about. What build system are you using ?

---

## Comment 3

> Username: ObjatieGroba  
> Created at: 2020-05-15 13:30:39 UTC  
> Updated at: 2020-05-15 13:41:09 UTC  
> Url: https://github.com/boostorg/python/issues/282#issuecomment-629237179  

Python 3.8 and boost 1.72 still.  
  
Is there any solutions?  
  
UPD:  
1) Make sure you have libpython3-dev and python3-dev installed.  
2) Make sure you have all necessary files.  
3) Done

---

## Comment 4

> Username: jakirkham  
> Created at: 2022-04-12 07:16:15 UTC  
> Url: https://github.com/boostorg/python/issues/282#issuecomment-1096227717  

The `m` postfix has to do with whether Python was built with `pymalloc` (default) or not. However as of Python 3.8 this distinction is moot, so the postfix was removed. Please see issue ( https://github.com/python/cpython/issues/80888 ) for details.
