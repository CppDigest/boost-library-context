# #102 fix boost::synchronized_value<>::load() [Merged]

> Username: rick68  
> Created at: 2016-10-22 08:14:33 UTC  
> Updated at: 2016-10-22 10:30:58 UTC  
> Merged at: 2016-10-22 10:30:40 UTC  
> Closed at: 2016-10-22 10:30:40 UTC  
> Url: https://github.com/boostorg/thread/pull/102  

The data member `boost::synchronized_value<>::value_` is not mutable, so boost::synchronized_value<>::load() could not be const.

---

## Comment 1

> Username: viboes  
> Created_at: 2016-10-22 10:30:58 UTC  
> Url: https://github.com/boostorg/thread/pull/102#issuecomment-255520701  

Thanks.   
Merged.

---
