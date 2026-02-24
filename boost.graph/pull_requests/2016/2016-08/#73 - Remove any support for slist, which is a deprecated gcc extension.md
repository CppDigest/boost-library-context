# #73 Remove any support for slist, which is a deprecated gcc extension. [Merged]

> Username: murraycu  
> Created at: 2016-08-09 08:53:50 UTC  
> Updated at: 2016-10-31 22:43:12 UTC  
> Merged at: 2016-10-31 22:43:12 UTC  
> Closed at: 2016-10-31 22:43:12 UTC  
> Url: https://github.com/boostorg/graph/pull/73  

Maybe this was useful once, but nobody should still be using the deprecated gcc extension std::slist now. Trying to support it clutters the code and can lead to warnings such as this:  
https://svn.boost.org/trac/boost/ticket/12048#comment:3  
  
slist seems to have been deprecated by gcc for a long time, so any BGL API that used it was implicitly deprecated too. So removing it now seems fine.  
  
We might want to add support for std::forward_list as a replacement for slist.

---
