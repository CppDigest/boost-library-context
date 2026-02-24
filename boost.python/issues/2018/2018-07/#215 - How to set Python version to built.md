# #215 - How to set Python version to built [Closed]

> Username: illera88  
> Created at: 2018-07-02 20:45:10 UTC  
> Updated at: 2018-07-03 00:51:05 UTC  
> Closed at: 2018-07-03 00:51:05 UTC  
> Url: https://github.com/boostorg/python/issues/215  

Hi,  
  
I'm using this:  
`.\b2.exe --with-python runtime-link=static toolset=msvc-14.1 link=static address-model=64 --build-type=complete`  
  
But the generated commands libraries seems to be using python27 and I want to use python36. How can I specify `b2` that I want to use python36 so the name of the generated library is something like:  
`libboost_python36-vc141-mt-sgd-x64-1_67.lib` instead of `libboost_python27-vc141-mt-sgd-x64-1_67.lib`  
  
cheers

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-07-02 20:53:44 UTC  
> Url: https://github.com/boostorg/python/issues/215#issuecomment-401931795  

The way to build `Boost.Python` with `Boost.Build` involves setting up the `tools` (including compiler and Python interpreter) in the ~/user-config.jam` file. You can add a line  
```  
using python : 3.6 : /path/to/the/python/executable ;  
```  
and then use `b2 python=3.6 ...` to select that. (That way you can have multiple Python instances configured in your `user-config.jam` file, and choose at build time which one to use. There are additional options to explicitly specify header include and library search paths, but the default should work fine.  
You may want to consult the `Boost.Build` docs for details.

---

## Comment 2

> Username: illera88  
> Created at: 2018-07-02 20:56:49 UTC  
> Url: https://github.com/boostorg/python/issues/215#issuecomment-401932811  

Im on a Windows system. What is the equivalent `~/user-config.jam` in windows?   
  
thank you

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2018-07-02 21:03:54 UTC  
> Url: https://github.com/boostorg/python/issues/215#issuecomment-401935541  

I don't understand. Why "equivalent" ? See https://boostorg.github.io/build/manual/develop/index.html#bbv2.overview.configuration

---

## Comment 4

> Username: illera88  
> Created at: 2018-07-03 00:51:05 UTC  
> Url: https://github.com/boostorg/python/issues/215#issuecomment-401979778  

using the `jam` file worked fine.   
  
thank you @stefanseefeld
