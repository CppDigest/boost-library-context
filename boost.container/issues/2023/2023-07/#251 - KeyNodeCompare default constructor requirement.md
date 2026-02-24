# #251 - KeyNodeCompare default constructor requirement [Closed]

> Username: stepanm-aparavi  
> Created at: 2023-07-26 12:27:43 UTC  
> Updated at: 2024-06-13 20:52:09 UTC  
> Closed at: 2024-06-13 20:52:09 UTC  
> Url: https://github.com/boostorg/container/issues/251  

Hi folks,  
  
I am trying to upgrade boost from 1.78.0 to 1.82.0. A blocking change is taking place, which breaks our compilation. `KeyNodeCompare`, when it is initializing for passing to iterators, stops taking a comparator of the container and becomes requiring a default constructor. It breaks what we rely on, as `key_compare` which we initializing the container with, is based on some complicated implementation, that must be shared among all `key_compare`-s within the container context.  
  
Could you give any advise, how to solve that?  
Thanks  
  
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
> Created at: 2023-07-26 12:50:49 UTC  
> Url: https://github.com/boostorg/container/issues/251#issuecomment-1651744144  

I just reverted `KeyNodeCompare()` to `KeyNodeCompare(key_comp())` in _tree.hpp@1.82.0_ wherever it was possible by comparison against _tree.hpp@1.78.0_ (see [fix-tree.patch](https://github.com/boostorg/container/files/12172479/fix-tree.patch)). It compiles. I continue testing and not hitting any run-time issues so far.  
  
Could you verify the patch, is it is possible, or advise anything else?  
Thanks

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-06-13 20:52:09 UTC  
> Url: https://github.com/boostorg/container/issues/251#issuecomment-2166770295  

Thanks for the report. This is the same issue that was recently fixed:  
  
https://github.com/boostorg/container/issues/275
