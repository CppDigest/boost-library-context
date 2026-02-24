# #139 Intel C++ compiler 17.0 workaround [Closed]

> Username: sergiud  
> Created at: 2016-10-23 17:51:17 UTC  
> Updated at: 2016-10-24 23:02:02 UTC  
> Closed at: 2016-10-24 23:02:02 UTC  
> Url: https://github.com/boostorg/fusion/pull/139  

This PR adds a workaround for Intel C++ compiler 17.0. Without the patch, I get the following errors:  
  
```  
boost/fusion/container/vector/vector.hpp(69): error: namespace "boost::fusion::vector_detail::result_of" has no member "value_at_c"  
          : boost::is_convertible<Sequence, typename result_of::value_at_c<This, 0>::type>  
                                                                ^  
boost/fusion/container/vector/vector.hpp(69): error: expected a ">"  
          : boost::is_convertible<Sequence, typename result_of::value_at_c<This, 0>::type>  
                                                                          ^  
boost/fusion/container/vector/vector.hpp(69): error: not a class or struct name  
          : boost::is_convertible<Sequence, typename result_of::value_at_c<This, 0>::type>  
            ^  
```

---

## Comment 1

> Username: djowel  
> Created_at: 2016-10-23 21:50:12 UTC  
> Url: https://github.com/boostorg/fusion/pull/139#issuecomment-255617019  

Please use the develop branch instead. Thanks!

---

## Comment 2

> Username: sergiud  
> Created_at: 2016-10-24 23:02:02 UTC  
> Url: https://github.com/boostorg/fusion/pull/139#issuecomment-255890300  

The issue has been fixed in the develop branch. Thanks.

---
