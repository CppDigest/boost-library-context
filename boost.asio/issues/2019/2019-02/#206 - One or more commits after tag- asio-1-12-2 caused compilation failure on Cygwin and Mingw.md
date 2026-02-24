# #206 - One or more commits after tag: asio-1-12-2 caused compilation failure on Cygwin and Mingw [Closed]

> Username: youngwolf-project  
> Created at: 2019-02-21 01:50:30 UTC  
> Updated at: 2019-02-28 11:58:58 UTC  
> Closed at: 2019-02-27 15:34:12 UTC  
> Url: https://github.com/boostorg/asio/issues/206  

Before, standalone asio can be compiled on 32bit Cygwin, 32 and 64bit Mingw.  
Now it cannot be compiled on all Cygwin and Mingw.

---

## Comment 1

> Username: tanzislam  
> Created at: 2019-02-25 08:16:34 UTC  
> Updated at: 2019-02-25 08:34:25 UTC  
> Url: https://github.com/boostorg/asio/issues/206#issuecomment-466912390  

@youngwolf-project What are the compilation errors that you're facing? I'm facing these:  
  
```  
C:/Extension/Programs/Programming/boost/boost/asio/windows/basic_random_access_handle.hpp:93:13: error: 'native_handle_type' does not name a type  
       const native_handle_type& handle)  
             ^~~~~~~~~~~~~~~~~~  
C:/Extension/Programs/Programming/boost/boost/asio/windows/basic_random_access_handle.hpp:93:13: note: (perhaps 'typename boost::asio::windows::basic_overlapped_handle<Executor>::native_handle_type' was intended)  
C:/Extension/Programs/Programming/boost/boost/asio/windows/basic_random_access_handle.hpp:113:13: error: 'native_handle_type' does not name a type  
       const native_handle_type& handle,  
             ^~~~~~~~~~~~~~~~~~  
C:/Extension/Programs/Programming/boost/boost/asio/windows/basic_random_access_handle.hpp:113:13: note: (perhaps 'typename boost::asio::windows::basic_overlapped_handle<Executor>::native_handle_type' was intended)  
```

---

## Comment 2

> Username: youngwolf-project  
> Created at: 2019-02-26 10:56:04 UTC  
> Url: https://github.com/boostorg/asio/issues/206#issuecomment-467393630  

@tanzislam   
Exactly.

---

## Comment 3

> Username: tanzislam  
> Created at: 2019-02-28 09:12:09 UTC  
> Url: https://github.com/boostorg/asio/issues/206#issuecomment-468195741  

This was fixed in 483f3d3 in "develop", though yet to reach the "master" branch of this project and the superproject.

---

## Comment 4

> Username: youngwolf-project  
> Created at: 2019-02-28 11:58:58 UTC  
> Url: https://github.com/boostorg/asio/issues/206#issuecomment-468246848  

@tanzislam Thanks, it's already available.
