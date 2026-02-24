# #31 Don't assume SIZEOF_LONG is undefined on cygwin [Closed]

> Username: corngood  
> Created at: 2015-08-04 18:51:45 UTC  
> Updated at: 2016-09-02 02:11:23 UTC  
> Closed at: 2016-09-02 02:11:23 UTC  
> Url: https://github.com/boostorg/python/pull/31  

SIZEOF_LONG is defined in pyconfig.h for python 2.7 in my version of cygwin.  More importantly it's defined as 8, so I was getting errors like:  
  
```  
In file included from /usr/include/python2.7/Python.h:58:0,  
                 from /cygdrive/c/Users/davidm/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp/BoostParts/boost/python/detail/wrap_python.hpp:144,  
                 from /cygdrive/c/Users/davidm/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp/BoostParts/boost/python/detail/prefix.hpp:13,  
                 from /cygdrive/c/Users/davidm/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp/BoostParts/boost/python/handle.hpp:8,  
                 from /cygdrive/c/Users/davidm/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp/BoostParts/boost/python/converter/arg_to_python_base.hpp:7,  
                 from /cygdrive/c/Users/davidm/.vim/bundle/YouCompleteMe/third_party/ycmd/cpp/BoostParts/libs/python/src/converter/arg_to_python_base.cpp:6:  
/usr/include/python2.7/pyport.h:886:2: error: #error "LONG_BIT definition appears wrong for platform (bad gcc/glibc config?)."  
 #error "LONG_BIT definition appears wrong for platform (bad gcc/glibc config?)."  
  ^  
```

---

## Comment 1

> Username: fatso83  
> Created_at: 2015-12-09 22:18:54 UTC  
> Url: https://github.com/boostorg/python/pull/31#issuecomment-163417480  

This should go away with #49. At least that error went away for me after applying that patch.

---
