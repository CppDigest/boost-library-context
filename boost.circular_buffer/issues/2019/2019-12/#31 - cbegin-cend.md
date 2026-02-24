# #31 - cbegin/cend? [Closed]

> Username: NAThompson  
> Created at: 2019-12-12 15:58:58 UTC  
> Updated at: 2019-12-12 16:07:32 UTC  
> Closed at: 2019-12-12 16:07:32 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/31  

Does it make sense to add `.cbegin()` and `.cend()` to `boost::circular_buffer`?  
  
It currently is not provided:  
  
```  
../boost/boost/math/statistics/univariate_statistics.hpp:82:19: error: ‘const class boost::circular_buffer<double>’ has no member named ‘cbegin’; did you mean ‘begin’?  
     return mean(v.cbegin(), v.cend());  
                 ~~^~~~~~  
                 begin  
```

---

## Comment 1

> Username: glenfe  
> Created at: 2019-12-12 16:02:06 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/31#issuecomment-565069737  

Yes, it makes sense to have them.

---

## Comment 2

> Username: NAThompson  
> Created at: 2019-12-12 16:07:32 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/31#issuecomment-565072061  

Cool; I'll prepare a PR.
