# #423 - Report the exception text from BOOST_CHECK_NO_THROW [Open]

> Username: ujos  
> Created at: 2024-07-10 10:40:28 UTC  
> Updated at: 2024-07-10 10:40:28 UTC  
> Url: https://github.com/boostorg/test/issues/423  

Source: https://stackoverflow.com/questions/15133259/boost-check-no-throw-how-to-get-exception-message-printed  
  
When I test a method using  
  
```cpp  
BOOST_CHECK_NO_THROW( method_to_test() );  
```  
  
and an exception is thrown, it displays that an exception was thrown, but never the exception's message like this:  
  
```  
test.cpp(14): error in "test": incorrect exception my_exception is caught  
```  
  
Is it possible to print the exception message as well, i.e. the string returned by `my_exception.what()`? `my_exception` is derived from `std::exception` and overloads `what()`.
