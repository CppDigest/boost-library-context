# #53 - List of non BOOST-prefixed macros [Closed]

> Username: ned14  
> Created at: 2019-12-16 13:58:48 UTC  
> Updated at: 2019-12-18 13:41:14 UTC  
> Closed at: 2019-12-18 13:41:14 UTC  
> Url: https://github.com/boostorg/iterator/issues/53  

The following macros are missing a BOOST_ prefix, which is against Boost library guidelines:  
  
```  
./boost/shared_container_iterator.hpp:#ifndef SHARED_CONTAINER_ITERATOR_RG08102002_HPP  
./boost/shared_container_iterator.hpp:#define SHARED_CONTAINER_ITERATOR_RG08102002_HPP  
./boost/shared_container_iterator.hpp:#endif  // SHARED_CONTAINER_ITERATOR_RG08102002_HPP  
```

---

## Comment 1

> Username: glenfe  
> Created at: 2019-12-18 13:41:14 UTC  
> Url: https://github.com/boostorg/iterator/issues/53#issuecomment-567035729  

Resolved in fdcd8439c08c2239110d73d9ffb211ffabf3919a.
