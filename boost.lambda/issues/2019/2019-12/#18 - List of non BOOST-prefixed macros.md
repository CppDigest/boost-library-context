# #18 - List of non BOOST-prefixed macros [Closed]

> Username: ned14  
> Created at: 2019-12-16 14:08:20 UTC  
> Updated at: 2019-12-24 23:55:36 UTC  
> Closed at: 2019-12-24 23:55:35 UTC  
> Url: https://github.com/boostorg/lambda/issues/18  

The following macros are missing a BOOST_ prefix, which is against Boost library guidelines. The corresponding Phoenix open issue is at https://github.com/boostorg/phoenix/issues/90  
  
```  
./boost/lambda/closures.hpp:#ifndef PHOENIX_CLOSURES_HPP  
./boost/lambda/closures.hpp:#define PHOENIX_CLOSURES_HPP  
```

---

## Comment 1

> Username: glenfe  
> Created at: 2019-12-24 23:55:35 UTC  
> Url: https://github.com/boostorg/lambda/issues/18#issuecomment-568811915  

Fixed in 8de6d9cb9fe9d486a5fd74fd9dd3dc805abab937.
