# #246 - Incorect usage of BOOST_HEADER_DEPRECATED [Closed]

> Username: Flamefire  
> Created at: 2019-12-23 12:15:20 UTC  
> Updated at: 2020-04-21 14:23:37 UTC  
> Closed at: 2020-03-30 07:02:16 UTC  
> Url: https://github.com/boostorg/test/issues/246  

The use in e.g. https://github.com/boostorg/test/blob/741ff88f56ef88e4db995e5478c978447757db07/include/boost/test/floating_point_comparison.hpp#L14 should simply pass the new header as the argument. Otherwise the message is:   
  
> boost/test/floating_point_comparison.hpp:14:1: warning: This header is deprecated. Use This header is deprecated. Please use <boost/test/tools/floating_point_comparison.hpp> instead. instead. [-W#pragma-messages]

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2020-03-30 07:02:16 UTC  
> Url: https://github.com/boostorg/test/issues/246#issuecomment-605820096  

Fixed in master
