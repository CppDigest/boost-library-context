# #81 Fix unused parameter warning in detail/signal_template.hpp [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2025-06-27 15:03:37 UTC  
> Updated at: 2025-06-27 16:24:16 UTC  
> Merged at: 2025-06-27 16:24:16 UTC  
> Closed at: 2025-06-27 16:24:16 UTC  
> Url: https://github.com/boostorg/signals2/pull/81  

Clang 20 with -Wall -Wextra -Werror and Boost 1.88 raises this warning/error:  
  
```  
/data/mwrep/res/osp/Boost/25-0-0-0/include/boost/signals2/detail/signal_template.hpp:778:75: error: unused parameter 'other' [-Werror,-Wunused-parameter]  
  778 |         const BOOST_SIGNALS2_SIGNAL_CLASS_NAME(BOOST_SIGNALS2_NUM_ARGS) & other) BOOST_NOEXCEPT  
      |                                                                           ^  
1 error generated.  
```

---
