# #169 - Fails to export PyInit_MODULE when compiling with hidden visibility [Closed]

> Username: xnox  
> Created at: 2017-10-30 11:19:07 UTC  
> Updated at: 2017-11-03 13:06:23 UTC  
> Closed at: 2017-11-03 13:06:23 UTC  
> Url: https://github.com/boostorg/python/issues/169  

It seems like `BOOST_PYTHON_USE_GCC_SYMBOL_VISIBILITY` is gone in `boost1.65.1` but is still referenced in `module_init.hpp`. This results in failure to export `PyInit_mymodule` when compiling with hidden visibility and g++.  
  
I have worked around this by add `-DBOOST_PYTHON_USE_GCC_SYMBOL_VISIBILITY=1` in my sources. But previous boost did the right thing here.  
  
Maybe the condition path `elif BOOST_PYTHON_USE_GCC_SYMBOL_VISIBILITY` can be dropped, adn the default fall through should be adjusted to become:  
```  
-extern "C" _BOOST_PYTHON_MODULE_INIT(name)  
+extern "C" BOOST_SYMBOL_EXPORT _BOOST_PYTHON_MODULE_INIT(name)  
```  
  
As `BOOST_SYMBOL_EXPORT` defines the right thing for the right compiler, for g++ it adds `__attribute__((__visibility__("default")))` no? or maybe use `BOOST_SYMBOL_VISIBLE` ?  
  
I will provide a short sample and build logs.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-10-30 12:06:42 UTC  
> Url: https://github.com/boostorg/python/issues/169#issuecomment-340425058  

Didn't https://github.com/boostorg/python/pull/159 address this ? Can you please confirm whether the bug is still present on the current "develop" branch ?

---

## Comment 2

> Username: xnox  
> Created at: 2017-10-30 23:18:43 UTC  
> Url: https://github.com/boostorg/python/issues/169#issuecomment-340613512  

It looks like it should. Let me verify that, and I guess I will have to cherrypick that patch into Debian and Ubuntu builds of boost1.65.1.

---

## Comment 3

> Username: xnox  
> Created at: 2017-11-03 13:06:23 UTC  
> Url: https://github.com/boostorg/python/issues/169#issuecomment-341697919  

Yeap, that patch is awesome, staged to be uploaded into Debian & Ubuntu. Thanks a lot!
