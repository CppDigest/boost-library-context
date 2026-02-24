# #543 Fix tuple helper classes [Open]

> Username: krwalker  
> Created at: 2026-01-20 20:41:02 UTC  
> Updated at: 2026-02-11 02:50:29 UTC  
> Url: https://github.com/boostorg/process/pull/543  

clang 19.1.5 reported the following error. Because the standard shows the helpers as structs, converted them all to structs instead of classes and removed unnecessary public visibility specifier.  
```  
Boost/libs/process/include/boost/process/v2/environment.hpp:1280:82: note: constrained by implicitly private inheritance here  
  1280 | class tuple_size<BOOST_PROCESS_V2_NAMESPACE::environment::key_value_pair_view> : integral_constant<std::size_t, 2u> {};  
       |                                                                                  ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
 /opt/gcc-15.1/bin/../lib/gcc/x86_64-pc-linux-gnu/15/../../../../include/c++/15/type_traits:94:28: note: member is declared here  
    94 |       static constexpr _Tp value = __v;  
       |                            ^  
```

---
