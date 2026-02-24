# #1071 - lboost_system is not getting built [Open]

> Username: C1Ron  
> Created at: 2025-08-06 11:22:55 UTC  
> Updated at: 2025-09-04 13:19:50 UTC  
> Url: https://github.com/boostorg/boost/issues/1071  

I'm trying to build boost 1.87 from source on Debian 12.2.0  
```  
git checkout boost-1.87.0  
./bootstrap.sh --with-libraries=system,thread  
sudo ./b2 install  
```  
I've also tried  
```  
sudo ./b2 install --with-system --with-thread  
```  
Then  
```  
find /usr/local/lib -name "libboost_thread*"  
  /usr/local/lib/libboost_thread.so.1.87.0  
  /usr/local/lib/cmake/boost_thread-1.87.0/libboost_thread-variant-static.cmake  
  /usr/local/lib/cmake/boost_thread-1.87.0/libboost_thread-variant-shared.cmake  
  /usr/local/lib/libboost_thread.so  
  /usr/local/lib/libboost_thread.a  
find /usr/local/lib -name "libboost_system*"  
# returns nothing  
```

---

## Comment 1

> Username: Nekto89  
> Created at: 2025-08-07 08:45:05 UTC  
> Url: https://github.com/boostorg/boost/issues/1071#issuecomment-3163135536  

it's header-only since Boost 1.69

---

## Comment 2

> Username: cho-m  
> Created at: 2025-08-13 14:30:58 UTC  
> Updated at: 2025-08-15 15:24:39 UTC  
> Url: https://github.com/boostorg/boost/issues/1071#issuecomment-3184178070  

Make sure to update git submodules after checking out tag.  
  
Boost.System 1.87 should have a stub library.  
Boost.System 1.89 (e.g. `master` branch submodules) will only install headers (EDIT: see https://github.com/boostorg/system/commit/7a495bb46d7ccd808e4be2a6589260839b0fd3a3)

---

## Comment 3

> Username: dacorsa  
> Created at: 2025-08-15 07:21:56 UTC  
> Url: https://github.com/boostorg/boost/issues/1071#issuecomment-3190830477  

i confirm that boost 1.89 not build "libboost_system" and 1.88 works well

---

## Comment 4

> Username: nigels-com  
> Created at: 2025-08-31 04:43:51 UTC  
> Url: https://github.com/boostorg/boost/issues/1071#issuecomment-3239755106  

Looks like this issue can be closed then?

---

## Comment 5

> Username: C1Ron  
> Created at: 2025-09-04 13:19:50 UTC  
> Url: https://github.com/boostorg/boost/issues/1071#issuecomment-3253689260  

Yes sure, but does the with-system flag do nothing then ?
