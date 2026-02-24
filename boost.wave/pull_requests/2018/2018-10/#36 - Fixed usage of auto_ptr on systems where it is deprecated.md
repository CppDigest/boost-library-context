# #36 Fixed usage of auto_ptr on systems where it is deprecated [Merged]

> Username: Kojoley  
> Created at: 2018-10-02 15:00:55 UTC  
> Updated at: 2018-10-02 15:12:54 UTC  
> Merged at: 2018-10-02 15:12:45 UTC  
> Closed at: 2018-10-02 15:12:45 UTC  
> Url: https://github.com/boostorg/wave/pull/36  

The macro `BOOST_NO_AUTO_PTR` is defined on systems where both `auto_ptr` and  
`unique_ptr` are available (and `auto_ptr` usage produces a deprecation warning).  
By using `BOOST_NO_CXX11_SMART_PTR` we will favor `unique_ptr` over `auto_ptr`.  
  
```  
slex/lexer.hpp:2423:13: error: 'template<class> class std::auto_ptr' is deprecated [-Werror=deprecated-declarations]  
```

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2018-10-02 15:12:38 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/36#pullrequestreview-160774157  

Thanks!

---
