# #33 - List of non BOOST-prefixed macros [Closed]

> Username: ned14  
> Created at: 2019-12-16 14:01:04 UTC  
> Updated at: 2019-12-18 13:35:47 UTC  
> Closed at: 2019-12-18 13:35:46 UTC  
> Url: https://github.com/boostorg/polygon/issues/33  

The following macros are missing a BOOST_ prefix, which is against Boost library guidelines:  
  
```  
./boost/polygon/gtl.hpp:#ifndef GTL_GTL_HPP  
./boost/polygon/gtl.hpp:#define GTL_GTL_HPP  
```

---

## Comment 1

> Username: glenfe  
> Created at: 2019-12-18 13:35:46 UTC  
> Url: https://github.com/boostorg/polygon/issues/33#issuecomment-567033680  

Resolved in 596ea34b36911f959d45f02089fd2bf462d9377b.
