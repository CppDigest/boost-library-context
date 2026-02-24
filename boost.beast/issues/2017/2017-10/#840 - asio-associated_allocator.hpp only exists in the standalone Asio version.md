# #840 - asio/associated_allocator.hpp only exists in the standalone Asio version [Closed]

> Username: theodelrieu  
> Created at: 2017-10-26 14:15:46 UTC  
> Updated at: 2017-10-26 14:35:30 UTC  
> Closed at: 2017-10-26 14:35:30 UTC  
> Url: https://github.com/boostorg/beast/issues/840  

Hi there.  
  
I've tried to build a small example program with the latest master version (127), that simply includes `boost/beast.hpp`:  
  
> fatal error: boost/asio/associated_allocator.hpp: No such file or directory  
  
It seems that this file was added on the standalone Asio repository 3 months ago, but is not in the latest Boost release (1.65.1).  
  
I feel like I've missed something obvious, since nobody reported it before...

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-10-26 14:24:10 UTC  
> Updated at: 2017-10-26 14:27:17 UTC  
> Url: https://github.com/boostorg/beast/issues/840#issuecomment-339683012  

If you're using the **master** branch of Beast, then you also need to use the **master** branch of all the other Boost libraries. There's no sane way for me to support Boost 1.65.1 anymore, since we are just about to release Boost 1.66.0 (which will include Beast). Your other option is to use Beast version 124 which is not updated for Net.TS-flavored Asio.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-10-26 14:29:39 UTC  
> Url: https://github.com/boostorg/beast/issues/840#issuecomment-339684821  

I have pushed the **v124** tag so you can easily check out the version of Beast which is not updated for Net.TS-flavored Asio: https://github.com/boostorg/beast/tree/v124

---

## Comment 3

> Username: theodelrieu  
> Created at: 2017-10-26 14:35:30 UTC  
> Url: https://github.com/boostorg/beast/issues/840#issuecomment-339686654  

That makes sense, looking forward to 1.66, meanwhile I'll be using v124. Thanks!
