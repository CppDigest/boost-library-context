# #493 - BOOST_HANA_ADAPT_STRUCT default max members decreased, breaking backwards compatibility [Closed]

> Username: joseph-ireland  
> Created at: 2021-08-18 07:13:48 UTC  
> Updated at: 2022-10-21 02:58:13 UTC  
> Closed at: 2022-10-21 02:58:13 UTC  
> Url: https://github.com/boostorg/hana/issues/493  

It seems that since commit b6807015f1d3bee2eb4d204, boost/hana/detail/struct_macros.hpp went from being generated with   
  
```  
export MAX_NUMBER_OF_MEMBERS=55; erb struct_macros.hpp.erb  
```  
  
to  
  
```  
erb struct_macros.hpp.erb  
```  
  
which resulted in the MAX_NUMBER_OF_MEMBERS defaulting to 40. This has broken backwards compatibility with older boost releases.  
  
Also the error for overflowing MAX_NUMBER_OF_MEMBERS is quite cryptic:   
```  
/path/to/example.cpp:904:1: error: pasting "BOOST_HANA_ADAPT_ADT_IMPL_" and "(" does not give a valid preprocessing token  
 BOOST_HANA_ADAPT_ADT(foo,  
 ^  
```  
Maybe the smaller macros like BOOST_HANA_PP_NARG could have a higher limit to get a more sensible error like   
```  
error: use of undeclared identifier 'BOOST_HANA_ADAPT_ADT_IMPL_46'  
```
