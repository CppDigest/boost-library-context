# #791 - boost-container: KeyNodeCompare default constructor requirement [Closed]

> Username: stepanm-aparavi  
> Created at: 2023-07-25 08:48:32 UTC  
> Updated at: 2023-07-26 12:30:03 UTC  
> Closed at: 2023-07-26 12:30:03 UTC  
> Url: https://github.com/boostorg/boost/issues/791  

Hi folks,  
  
I am trying to upgrade boost from 1.78.0 to 1.82.0. A blocking change is taking place, which breaks our compilation. `KeyNodeCompare`, when it is initializing for passing to iterators, stops taking a comparator of the container to and becomes requiring a default constructor. It breaks what we rely on, as `key_compare` which we initializing the container with, is based on some complicated implementation, that must be shared among all `key_compare`-s within the container context.  
  
Could you give any advise, how to solve that?  
  
_boost/container/detail/tree.hpp@1.78.0_  
```  
   template <class K>  
   BOOST_CONTAINER_ATTRIBUTE_NODISCARD BOOST_CONTAINER_FORCEINLINE  
      typename dtl::enable_if_transparent<key_compare, K, iterator>::type  
         find(const K& k)  
   {  return iterator(this->icont().find(k, KeyNodeCompare(key_comp())));  }  
```  
_boost/container/detail/tree.hpp@1.82.0_  
```  
   template <class K>  
   BOOST_CONTAINER_ATTRIBUTE_NODISCARD BOOST_CONTAINER_FORCEINLINE  
      typename dtl::enable_if_transparent<key_compare, K, iterator>::type  
         find(const K& k)  
   {  return iterator(this->icont().find(k, KeyNodeCompare()));  }  
```

---

## Comment 1

> Username: stepanm-aparavi  
> Created at: 2023-07-26 09:52:12 UTC  
> Updated at: 2023-07-26 09:52:35 UTC  
> Url: https://github.com/boostorg/boost/issues/791#issuecomment-1651404905  

[boostorg / container](https://github.com/boostorg/container/issues) would be the better place for this issue. Can someone transfer this issue to that repo?

---

## Comment 2

> Username: stepanm-aparavi  
> Created at: 2023-07-26 12:30:02 UTC  
> Url: https://github.com/boostorg/boost/issues/791#issuecomment-1651712298  

Re-opened [boostorg/container/issues/251](https://github.com/boostorg/container/issues/251)
