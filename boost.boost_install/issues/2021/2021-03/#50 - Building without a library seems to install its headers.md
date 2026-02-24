# #50 - Building without a library seems to install its headers [Closed]

> Username: mablanchard  
> Created at: 2021-03-24 11:36:31 UTC  
> Updated at: 2021-03-24 15:16:14 UTC  
> Closed at: 2021-03-24 15:16:14 UTC  
> Url: https://github.com/boostorg/boost_install/issues/50  

I'm trying to partially build Boost 1.75.0 by passing using `b2`'s `--without-<library>` option:  
  
```  
  --without-<library>     Do not build, stage, or install the specified  
                          <library>. By default, all libraries are built.  
```  
For example for the `log` library:  
  
```  
./b2 --without-log  
```  
The configuration reports gives:  
```  
    - log                      : not building  
```  
This prevent the `libboost_log` from being built and installed (which is indeed expected) but it the `log` headers at `include/boost/log/` still get installed by `./b2 install`... Is that expected or am I missing something here?

---

## Comment 1

> Username: pdimov  
> Created at: 2021-03-24 15:02:27 UTC  
> Url: https://github.com/boostorg/boost_install/issues/50#issuecomment-805896588  

It's expected. The current Boost architecture, for backward compatibility, is that headers in a release are all copied into the `boost/` top-level directory, deleted from their original locations in `libs/<libname>/include`, and from then on, there is no way to determine which header belongs to which library. The `--without` option only applies to what is built, and not to what headers are installed. (It also doesn't apply to header-only libraries.)  
  
This is also reflected in downstream packaging, e.g. on Ubuntu you have libboost-dev which contains all the headers, and libboost-log which contains the built Log library.

---

## Comment 2

> Username: mablanchard  
> Created at: 2021-03-24 15:16:14 UTC  
> Url: https://github.com/boostorg/boost_install/issues/50#issuecomment-805912174  

Ok, thanks for the quick reply!
