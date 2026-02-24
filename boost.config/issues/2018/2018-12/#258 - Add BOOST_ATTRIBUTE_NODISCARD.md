# #258 - Add BOOST_ATTRIBUTE_NODISCARD [Closed]

> Username: HDembinski  
> Created at: 2018-12-17 14:56:16 UTC  
> Updated at: 2019-04-03 07:47:11 UTC  
> Closed at: 2019-04-02 17:47:21 UTC  
> Url: https://github.com/boostorg/config/issues/258  

Similar to BOOST_ATTRIBUTE_UNUSED, it would be nice to have a macro which adds the `nodiscard` attribute when that is available.  
  
```  
#if __cplusplus >= 201703L  
#define BOOST_ATTRIBUTE_NODISCARD [[nodiscard]]  
#else  
#define BOOST_ATTRIBUTE_NODISCARD  
#endif  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-04-02 17:47:21 UTC  
> Url: https://github.com/boostorg/config/issues/258#issuecomment-479116447  

Now in develop.

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-04-03 07:47:11 UTC  
> Url: https://github.com/boostorg/config/issues/258#issuecomment-479378260  

Cool, thank you!
