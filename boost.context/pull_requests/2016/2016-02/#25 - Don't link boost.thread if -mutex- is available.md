# #25 Don't link boost.thread if <mutex> is available. [Merged]

> Username: Flast  
> Created at: 2016-02-05 07:30:51 UTC  
> Updated at: 2016-02-13 01:52:28 UTC  
> Merged at: 2016-02-12 07:18:01 UTC  
> Closed at: 2016-02-12 07:18:01 UTC  
> Url: https://github.com/boostorg/context/pull/25  

Linking boost.thread is unnecessary if the std library supports std::call_once.  
  
Tested: Fedora Rawhide x86_64 / GCC 6.0.0 20160201

---

## Comment 1

> Username: olk  
> Created_at: 2016-02-12 07:18:03 UTC  
> Url: https://github.com/boostorg/context/pull/25#issuecomment-183211364  

nice, thx

---
