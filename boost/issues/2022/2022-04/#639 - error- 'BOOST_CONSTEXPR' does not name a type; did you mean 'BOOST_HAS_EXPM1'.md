# #639 - error: 'BOOST_CONSTEXPR' does not name a type; did you mean 'BOOST_HAS_EXPM1'? [Open]

> Username: sunstarchan  
> Created at: 2022-04-12 10:35:49 UTC  
> Updated at: 2022-04-12 10:35:49 UTC  
> Url: https://github.com/boostorg/boost/issues/639  

I'm building a program with stdc++17, which depends on `pcl/search/kdtree.h`. I got following errors:  
```  
In file included from /usr/include/boost/mpl/not.hpp:17:0,  
                 from /usr/include/boost/mpl/assert.hpp:17,  
                 from /usr/local/include/pcl-1.11/pcl/point_struct_traits.h:42,  
                 from /usr/local/include/pcl-1.11/pcl/type_traits.h:40,  
                 from /usr/local/include/pcl-1.11/pcl/memory.h:46,  
                 from /usr/local/include/pcl-1.11/pcl/pcl_base.h:46,  
                 from /usr/local/include/pcl-1.11/pcl/search/search.h:41,  
                 from /usr/local/include/pcl-1.11/pcl/search/kdtree.h:42,  
                 from modules/pp_preprocess/opt_exec.cpp:4:  
/usr/include/boost/mpl/bool.hpp:29:5: error: 'BOOST_CONSTEXPR' does not name a type; did you mean 'BOOST_HAS_EXPM1'?  
     BOOST_CONSTEXPR operator bool() const { return this->value; }  
```  
  
It seems that `BOOST_CONSTEXPR` is not for stdc++17, or should I build boost from source with stdc++17?
