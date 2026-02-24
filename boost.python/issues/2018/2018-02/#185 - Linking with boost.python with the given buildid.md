# #185 - Linking with boost.python with the given buildid [Closed]

> Username: KirillSuetnov  
> Created at: 2018-02-06 13:05:16 UTC  
> Updated at: 2018-04-09 01:23:51 UTC  
> Closed at: 2018-04-09 01:23:51 UTC  
> Url: https://github.com/boostorg/python/issues/185  

I've built boost.python with the following command:  
  
b2 --with-python --python-buildid=36 --build-dir=build64-msvc-14.0 --stagedir=./lib64-msvc-14.0 toolset=msvc-14.0 variant=release,debug link=shared,static threading=multi runtime-link=shared address-model=64 -j5 -d2 -opython36-build.log  
  
Built libraries looks like:  
libboost_python3-36-vc140-mt-gd-x64-1_66.lib  
  
Name is correct.  
  
But boost.python and auto_link.hpp knowns nothing about --python-buildid:  
here is line from auto_link.hpp:  
pragma comment(lib, BOOST_LIB_PREFIX BOOST_STRINGIZE(BOOST_LIB_NAME) "-" BOOST_LIB_TOOLSET BOOST_LIB_THREAD_OPT BOOST_LIB_RT_OPT BOOST_LIB_ARCH_AND_MODEL_OPT "-" BOOST_LIB_VERSION ".lib")  
  
and this is how pyhton lib name is constructed:  
```  
#if PY_MAJOR_VERSION == 2  
#  define BOOST_LIB_NAME boost_python  
#elif PY_MAJOR_VERSION == 3  
#  define BOOST_LIB_NAME boost_python3  
#endif  
```  
The only way to fix it (except renaming library itself manually) is to define BOOST_PYTHON_NO_LIB and specify library name manualy- that's ugly.  
  
I suggest to make simple patch for boost.python's config.hpp:  
```  
#if PY_MAJOR_VERSION == 2  
#  define BOOST_LIB_NAME BOOST_PP_CAT(boost_python, BOOST_PYTHON_BUILDID)  
#elif PY_MAJOR_VERSION == 3  
#  define BOOST_LIB_NAME BOOST_PP_CAT(boost_python3, BOOST_PYTHON_BUILDID)  
#endif  
```  
If BOOST_PYTHON_BUILDID is defined, it adds to libname it's buildid, and all links ok.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-02-06 13:13:25 UTC  
> Url: https://github.com/boostorg/python/issues/185#issuecomment-363418188  

Why do you want to add a "build ID" to begin with ? Do you expect to host libraries for multiple Python versions in the same directory ?

---

## Comment 2

> Username: KirillSuetnov  
> Created at: 2018-02-06 13:38:56 UTC  
> Url: https://github.com/boostorg/python/issues/185#issuecomment-363424566  

Yes, I expect to put all python libraries into one build directory.   
But it doesn't matter: if you put them in different ones, you'll face the same problem with --python-buildid: it modifies library's name in the way which  is not handled by current boost headers

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2018-02-06 13:41:13 UTC  
> Url: https://github.com/boostorg/python/issues/185#issuecomment-363425133  

Right. If we encode the Python version into the name (instead of simply using `boost_python` and `boost_python3`), you wouldn't need a build ID in the name any longer. Wouldn't that be a better (simpler and more elegant) solution to your problem ?

---

## Comment 4

> Username: KirillSuetnov  
> Created at: 2018-02-06 14:58:20 UTC  
> Url: https://github.com/boostorg/python/issues/185#issuecomment-363447735  

Cool, it looks more elegant, if version number from 'using python : 3.4 : ...' boost bjamfile appeares in library name without '--python-buildid' help.   
Also, this solution makes redundant --python-buildid at all.

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2018-02-14 12:55:48 UTC  
> Url: https://github.com/boostorg/python/issues/185#issuecomment-365598955  

I have merged https://github.com/boostorg/python/commit/d4d41d94aecc9f8098aabd3587fcb95458451f71 to support this. As a consequence, Boost.Python now uses a python version suffix to disambiguate between different Python versions. (There still is the above `--python-buildid` in case you want to build against multiple Python variants, such as debug vs. release).  
Please give this a try (using the `develop` branch for now) and report whether it works for you, so I we can close this issue.
