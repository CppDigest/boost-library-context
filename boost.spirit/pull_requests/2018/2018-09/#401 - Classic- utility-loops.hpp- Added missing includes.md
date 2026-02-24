# #401 Classic: utility/loops.hpp: Added missing includes [Merged]

> Username: Kojoley  
> Created at: 2018-09-26 13:21:15 UTC  
> Updated at: 2018-10-08 10:56:37 UTC  
> Merged at: 2018-09-27 18:24:34 UTC  
> Closed at: 2018-09-27 18:24:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/401  

```  
In file included from typeof_support/typeof_utility.cpp:13:  
In file included from ../../../../boost/spirit/include/classic_utility.hpp:11:  
In file included from ../../../../boost/spirit/home/classic/utility.hpp:30:  
../../../../boost/spirit/home/classic/utility/loops.hpp:269:30: error: no template named 'if_' in namespace 'boost::mpl'; did you mean 'move_detail::if_'?  
            typedef typename mpl::if_<  
                             ^~~~~~~~  
                             move_detail::if_  
../../../../boost/move/detail/meta_utils_core.hpp:49:8: note: 'move_detail::if_' declared here  
struct if_ : if_c<0 != T1::value, T2, T3>  
       ^  
1 error generated.  
```

---
