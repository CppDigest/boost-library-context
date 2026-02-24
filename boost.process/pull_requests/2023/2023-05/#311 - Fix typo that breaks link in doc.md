# #311 Fix typo that breaks link in doc [Merged]

> Username: slowriot  
> Created at: 2023-05-05 12:04:55 UTC  
> Updated at: 2023-08-14 10:01:42 UTC  
> Merged at: 2023-08-14 10:01:42 UTC  
> Closed at: 2023-08-14 10:01:42 UTC  
> Url: https://github.com/boostorg/process/pull/311  

Trivial change: An external URL in this documentation is prefixed by a `(`, which breaks the link  
  
As seen on `https://www.boost.org/doc/libs/1_82_0/doc/html/boost_process/concepts.html`, the link points erroneously to `https://www.boost.org/doc/libs/1_82_0/doc/html/boost_process/(http:/pubs.opengroup.org/onlinepubs/009695399/functions/mkfifo.html`.

---
