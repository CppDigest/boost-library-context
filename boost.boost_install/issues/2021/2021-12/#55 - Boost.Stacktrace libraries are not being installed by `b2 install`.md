# #55 - Boost.Stacktrace libraries are not being installed by `b2 install` [Closed]

> Username: Lastique  
> Created at: 2021-12-08 20:12:15 UTC  
> Updated at: 2022-01-29 13:58:44 UTC  
> Closed at: 2021-12-08 20:30:54 UTC  
> Url: https://github.com/boostorg/boost_install/issues/55  

Running the following command line:  
  
```  
bjam -j 8 --prefix="$HOME/boost_install_root" toolset=gcc variant=release threading=multi debug-symbols=on runtime-link=shared numa=on link=static,shared --with-stacktrace install  
```  
  
does not result in Boost.Stacktrace libraries installed in `$HOME/boost_install_root`. The library is being successfully built, and its libraries are present in `$BOOST_ROOT/bin.v2`, but the only thing that is installed is CMake config files - no headers, no libraries.  
  
This is a regression in 1.78, 1.77 worked fine. Boost.Stacktrace hasn't changed in this regard between releases, so my current suspects are Boost.Install or Boost.Build.  
  
This is on Kubuntu 21.10.  
  
Note that for other libraries, like Boost.Atomic for example, the above command works as intended.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-12-08 20:30:53 UTC  
> Url: https://github.com/boostorg/boost_install/issues/55#issuecomment-989172310  

Sorry, this seems to be a duplicate of https://github.com/bfgroup/b2/issues/104.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-01-29 13:58:43 UTC  
> Url: https://github.com/boostorg/boost_install/issues/55#issuecomment-1024916904  

Looks like this also affects Fiber: https://github.com/boostorg/fiber/issues/290.
