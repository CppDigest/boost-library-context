# #40 - Runtime error. [Open]

> Username: fynkxo  
> Created at: 2025-10-22 07:45:39 UTC  
> Updated at: 2025-10-22 07:45:39 UTC  
> Url: https://github.com/boostorg/contract/issues/40  

I encountered a runtime error:  
  
When I write:  
```  
auto check = boost::contract::public_function(this)  
    .precondition([&]() { BOOST_CONTRACT_ASSERT(is_double() || is_unknown()); });  
```  
it throws the error:  
"Assertion failed: BOOST_CONTRACT_ERROR_missing_check_object_declaration"  
at runtime.  
  
However, when I change it to:  
```  
boost::contract::check check = boost::contract::public_function(this)  
    .precondition([&]() { BOOST_CONTRACT_ASSERT(is_double() || is_unknown()); });  
```  
there is no error.  
  
Do I have to write it the second way?
