# #17 fix a bug in boost::signals2::detail::operator<=() [Merged]

> Username: rick68  
> Created at: 2016-11-09 18:06:08 UTC  
> Updated at: 2016-11-09 22:33:53 UTC  
> Merged at: 2016-11-09 22:33:53 UTC  
> Closed at: 2016-11-09 22:33:53 UTC  
> Url: https://github.com/boostorg/signals2/pull/17  

`l <= r` equals `!(l > r)`

---
