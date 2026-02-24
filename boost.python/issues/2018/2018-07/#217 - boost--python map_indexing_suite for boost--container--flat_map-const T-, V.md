# #217 - boost::python map_indexing_suite for boost::container::flat_map<const T*, V> [Open]

> Username: finjulhich  
> Created at: 2018-07-07 10:31:41 UTC  
> Updated at: 2018-07-09 01:47:09 UTC  
> Url: https://github.com/boostorg/python/issues/217  

Hello,  
  
I've tried to adapt  
  
`using myfm  =  boost::container::flat_map<const T*, V>;`  
  
with map_indexing_suite  
  
```  
  class_< myfm, myfm*  >("myfm")  
    .def( map_indexing_suite<myfm>{} )  
  ;  
```  
  
I get a compile errors:  
/usr/include/boost/python/detail/invoke.hpp:75:14: error: no match for call to ‘(const boost::python::detail::specify_a_return_value_policy_to_wrap_functions_returning<const T*>) (const T*)’  
  
what does it mean?

---

## Comment 1

> Username: finjulhich  
> Created at: 2018-07-09 01:47:09 UTC  
> Url: https://github.com/boostorg/python/issues/217#issuecomment-403337030  

I can't find any function that returns key_type in flat_map!
