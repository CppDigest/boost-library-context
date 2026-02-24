# #1362 - File missing: empty_value.hpp [Closed]

> Username: NipunRamani99  
> Created at: 2018-12-10 06:59:14 UTC  
> Updated at: 2018-12-14 16:00:34 UTC  
> Closed at: 2018-12-14 16:00:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1362  

### Version of Beast  
197  
  
###Compiler  
I am using MSVC on VS2017  
  
I am missing a file called core/empty_value.hpp when I am building the .lib using VS2017.   
Can anyone tell me which dependency does it belong to?  
I am using boost 1.66 which I installed via vcpkg.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-10 14:55:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1362#issuecomment-445843668  

If you are using Boost 1.66 then you need to use the version of Beast that comes with that version of Boost, which is version 144-hf1 and located in the Boost package which you installed. In order to use Beast version 197, which is the currently the tip of the **develop** branch, then you would also need to use the **develop** branch of Boost. You can do this by cloning the repository for the Boost super-project (https://github.com/boostorg/boost) and initializing the submodules:  
  
```  
git clone git@github.com:boostorg/boost.git  
cd boost  
git checkout develop  
git submodule init  
git submodule update --recursive  
```

---

## Comment 2

> Username: NipunRamani99  
> Created at: 2018-12-14 16:00:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1362#issuecomment-447368419  

Got it, took me a while to figure out how to use the library.
