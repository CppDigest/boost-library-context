# #178 Check for overflow in basic_parser [Merged]

> Username: sdkrystian  
> Created at: 2020-08-20 16:35:15 UTC  
> Updated at: 2020-08-22 18:00:44 UTC  
> Merged at: 2020-08-20 18:45:54 UTC  
> Closed at: 2020-08-20 18:45:54 UTC  
> Url: https://github.com/boostorg/json/pull/178  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-20 16:36:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/178#pullrequestreview-471796898  

📁 include/boost/json/basic_parser.hpp

```diff
 207 |+         sizeof(std::size_t) +
 208 |+         // comment/utf8 state
 209 |+         sizeof(state));
```

> Username: vinniefalco  
> Created_at: 2020-08-20 16:36:44 UTC  
> Updated_at: 2020-08-20 17:22:05 UTC  
> Url: https://github.com/boostorg/json/pull/178#discussion_r474119094  

this is harder to read please put the comments back on the same line. reformat line 203 thusly:  
```  
(sizeof(state) +  
    sizeof(std::size_t)) * depth() +  // array and object state + size  
```


---
