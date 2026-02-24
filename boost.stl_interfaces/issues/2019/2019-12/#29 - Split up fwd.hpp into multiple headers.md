# #29 - Split up fwd.hpp into multiple headers [Closed]

> Username: badair  
> Created at: 2019-12-26 07:27:46 UTC  
> Updated at: 2019-12-29 19:57:14 UTC  
> Closed at: 2019-12-29 19:57:14 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/29  



---

## Comment 1

> Username: tzlaine  
> Created at: 2019-12-29 19:57:13 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/29#issuecomment-569536856  

I'm not going to do this one.  That header is very small (~100 lines).  It's no burden to include it.  Moreover, it has at least some content needed by all the other headers, so splitting it up would not even reduce the number of headers you need to include to use any one header -- that number would remain at 2.
