# #47 - Single header version of mp11 [Open]

> Username: oliora  
> Created at: 2020-04-10 12:38:11 UTC  
> Updated at: 2023-11-06 14:16:46 UTC  
> Url: https://github.com/boostorg/mp11/issues/47  

It would be great to have boost.mp11 available as a single header with root namespace that can be defined from outside. This allows to distribute mp11 along with a library that needs MPL but does not otherwise depend on Boost. Having a definable namespace allows each separate library to distribute its own copy of mp11 without conflicts. For example I’d like to use mp11 in my [Ppconsul](https://github.com/oliora/ppconsul) project without introducing a dependency on *modern* Boost (one is not available on some platforms where Ppconsul is used). Currently Ppconsul uses Boost.MPL and Boost.Fusion which I want to get rid of in favor of some modern MPL library as formers are slow to compile and harder to use.  
  
Other modern MPL libraries usually have a single header version ( I guess for exact same reason).  
  
If there is a general interest in getting a single header mp11 I can implement it.

---

## Comment 1

> Username: glenfe  
> Created at: 2020-04-10 12:55:37 UTC  
> Url: https://github.com/boostorg/mp11/issues/47#issuecomment-612016900  

Mp11 does not depend on any other Boost library and can be shipped independently. i.e. Users don't need a Boost distribution, they can clone (or you can package) mp11 from this repository.

---

## Comment 2

> Username: oliora  
> Created at: 2020-04-10 13:12:06 UTC  
> Updated at: 2020-04-10 13:14:23 UTC  
> Url: https://github.com/boostorg/mp11/issues/47#issuecomment-612022199  

@glenfe that’s not exactly what I need for my project. I have a public header dependency on mp11 so if I ship my library binary and headers (so apps can be build against it) I need to either ask user to install boost or ship mp11 along with my headers. Although I agree it’s not a huge issue as it’s only compile time dependency on Boost (or just mp11) for end users of my projects.

---

## Comment 3

> Username: glenfe  
> Created at: 2020-04-10 13:25:26 UTC  
> Url: https://github.com/boostorg/mp11/issues/47#issuecomment-612027067  

> I need to either ask user to install boost or ship mp11 along with my headers  
  
There are three options, not two:  
* You could ship Mp11 with your library  
* Your users could install just Mp11 by getting it from this repository  
* Your users could install Boost  
  
i.e. If all of Boost is too much for them, they can get Mp11 alone.

---

## Comment 4

> Username: oliora  
> Created at: 2020-04-10 13:48:11 UTC  
> Url: https://github.com/boostorg/mp11/issues/47#issuecomment-612036054  

That’s correct. But I’d prefer to use option 1 so users need less to install.

---

## Comment 5

> Username: pdimov  
> Created at: 2020-04-10 14:30:36 UTC  
> Url: https://github.com/boostorg/mp11/issues/47#issuecomment-612054020  

Related: https://github.com/boostorg/mp11/commit/f24d1ca5df53fe9f70ae359a3b8ecd4bb86bcef2

---

## Comment 6

> Username: oliora  
> Created at: 2020-04-10 14:34:02 UTC  
> Url: https://github.com/boostorg/mp11/issues/47#issuecomment-612055455  

@pdimov What was the rational behind the removal?

---

## Comment 7

> Username: pdimov  
> Created at: 2020-04-10 14:34:52 UTC  
> Url: https://github.com/boostorg/mp11/issues/47#issuecomment-612055856  

I got tired of regenerating the header on every change. :-)

---

## Comment 8

> Username: pdimov  
> Created at: 2020-04-10 14:37:50 UTC  
> Url: https://github.com/boostorg/mp11/issues/47#issuecomment-612057100  

But feel free to use the script and generate your own copy to distribute.  
  
My main motivation for having the single header was to be able to include it on Godbolt, and once mp11 became part of Boost and Godbolt acquired support for libraries, Boost included, this was no longer an issue.

---

## Comment 9

> Username: oliora  
> Created at: 2020-04-10 14:51:50 UTC  
> Url: https://github.com/boostorg/mp11/issues/47#issuecomment-612063016  

Thank you for the pointer! I guess I can automate it to have latest and greatest single header.

---

## Comment 10

> Username: HDembinski  
> Created at: 2020-05-17 11:15:12 UTC  
> Url: https://github.com/boostorg/mp11/issues/47#issuecomment-629780655  

Including individual boost libs as git submodules works great.  If you don't like git submodules, CMake can also download individual libraries for you at build time including mp11.  
  
From boost.histograms's CMakeLists.txt:  
```  
  file(DOWNLOAD  
    "https://raw.githubusercontent.com/boostorg/cmake/develop/include/BoostFetch.cmake"  
    "${CMAKE_BINARY_DIR}/fetch_and_include/BoostFetch.cmake"  
  )  
  
  include("${CMAKE_BINARY_DIR}/fetch_and_include/BoostFetch.cmake")  
  
  boost_fetch(boostorg/cmake TAG develop NO_ADD_SUBDIR)  
  
  FetchContent_GetProperties(boostorg_cmake)  
  
  list(APPEND CMAKE_MODULE_PATH ${boostorg_cmake_SOURCE_DIR}/include)  
  
  boost_fetch(boostorg/mp11 TAG develop EXCLUDE_FROM_ALL)  
```

---

## Comment 11

> Username: vinniefalco  
> Created at: 2023-11-06 14:16:46 UTC  
> Url: https://github.com/boostorg/mp11/issues/47#issuecomment-1794928127  

There is one very worthwhile option which has not been discussed: drop support for any platform that does not support the latest Boost Libraries.
