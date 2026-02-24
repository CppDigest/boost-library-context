# #42 - std::exception_ptr integration doesn't seem to work correctly when the exception is derived from boost::exception [Closed]

> Username: pdimov  
> Created at: 2022-01-25 19:30:13 UTC  
> Updated at: 2022-01-26 05:32:44 UTC  
> Closed at: 2022-01-26 05:32:44 UTC  
> Url: https://github.com/boostorg/exception/issues/42  

See https://godbolt.org/z/dofa6bbcc.  
  
This exception type  
```  
class my_exception: public std::exception  
{  
};  
```  
can be caught with `current_exception` and rethrown properly by `rethrow_exception`:  
```  
    try  
    {  
        throw my_exception();  
    }  
    catch( ... )  
    {  
        boost::exception_ptr p = boost::current_exception();  
  
        BOOST_TEST_THROWS( boost::rethrow_exception( p ), my_exception );  
    }  
```  
  
However if we add a `boost::exception` base,  
```  
class my_exception2: public std::exception, public boost::exception  
{  
};  
```  
`rethrow_exception` no longer throws `my_exception2`:  
```  
    try  
    {  
        throw my_exception2();  
    }  
    catch( ... )  
    {  
        boost::exception_ptr p = boost::current_exception();  
  
        BOOST_TEST_THROWS( boost::rethrow_exception( p ), my_exception2 );  
        BOOST_TEST_THROWS( boost::rethrow_exception( p ), boost::exception );  
    }  
```  
even though it does throw something derived from `boost::exception`:  
```  
example.cpp(33): expression 'boost::rethrow_exception( p )' did not throw exception 'my_exception2' in function 'int main()'  
1 error detected.  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2022-01-25 19:39:11 UTC  
> Updated at: 2022-01-25 19:39:48 UTC  
> Url: https://github.com/boostorg/exception/issues/42#issuecomment-1021542174  

Same problem exists with exceptions derived from some other standard exception type, such as `std::logic_error`: https://godbolt.org/z/e73Moo5MP  
  
It looks like the code starting here: https://github.com/boostorg/exception/blob/b8e9e98b33a18e020e0452497a47ca383e0a616d/include/boost/exception/detail/exception_ptr.hpp#L379  
  
isn't correct; `std::current_exception()` should be used first, when available, not as a last resort.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-01-25 23:14:20 UTC  
> Url: https://github.com/boostorg/exception/issues/42#issuecomment-1021696779  

See #43.
