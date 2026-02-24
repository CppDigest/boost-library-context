# #21 - Some CMake files for Python and NumPy lack version suffix [Closed]

> Username: jbeich  
> Created at: 2019-10-30 21:42:42 UTC  
> Updated at: 2020-05-06 19:26:28 UTC  
> Closed at: 2020-05-06 19:26:28 UTC  
> Url: https://github.com/boostorg/boost_install/issues/21  

Downstream may need Python 2 and Python 3 to coexist on an installed system. However, doing so would override the following files:  
```  
lib/cmake/boost_numpy-1.72.0/boost_numpy-config-version.cmake  
lib/cmake/boost_numpy-1.72.0/boost_numpy-config.cmake  
lib/cmake/boost_python-1.72.0/boost_python-config-version.cmake  
lib/cmake/boost_python-1.72.0/boost_python-config.cmake  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2019-10-30 22:18:25 UTC  
> Url: https://github.com/boostorg/boost_install/issues/21#issuecomment-548136701  

These files are shared between Python 2 and Python 3 libraries.
