# #106 - Fix behavior of 1d histograms: containers should not be unpacked when passed to operator() [Closed]

> Username: HDembinski  
> Created at: 2018-09-26 08:14:05 UTC  
> Updated at: 2018-10-08 05:41:49 UTC  
> Closed at: 2018-10-08 05:41:49 UTC  
> Url: https://github.com/boostorg/histogram/issues/106  

Steven Watanabe  
> I can pass  
a one-element tuple to a dynamic histogram  
and it will be unpacked.  Similarly, I can  
pass a (one element) vector to a (one axis)  
dynamic histogram and it will work.  The  
latter has to be supported as the size cannot  
be detected at compile time.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-10-08 05:41:49 UTC  
> Url: https://github.com/boostorg/histogram/issues/106#issuecomment-427726292  

fixed in develop
