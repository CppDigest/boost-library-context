# #282 Fix errors due to non-explicit conversions of tribool to bool [Open]

> Username: DenizThatMenace  
> Created at: 2020-09-03 10:56:04 UTC  
> Updated at: 2024-05-31 08:43:29 UTC  
> Url: https://github.com/boostorg/test/pull/282  

This fixes errors like the following that occur with newer compiler versions:  
  
```  
/usr/include/boost/test/tools/assertion_result.hpp:57:79: error: no matching function for call to ‘boost::test_tools::assertion_result::readonly_property65::readonly_property65(boost::logic::tribool)’  
   57 |     assertion_result( BoolConvertable const& pv_ ) : p_predicate_value( !!pv_ ) {}  
      |                                                                               ^  
```

---
