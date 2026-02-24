# #2 - Put safety checks in exception_checker [Closed]

> Username: akrzemi1  
> Created at: 2023-04-23 15:33:13 UTC  
> Updated at: 2023-04-23 21:44:25 UTC  
> Closed at: 2023-04-23 21:41:49 UTC  
> Url: https://github.com/boostorg/scope/issues/2  

In the destructor of excepiton checker  
https://github.com/Lastique/scope/blob/develop/include/boost/scope/exception_checker.hpp#L81  
put an assertion:  
  
```c++  
    result_type operator()() const noexcept  
    {  
        int new_uncaugth_count = boost::core::uncaught_exceptions();  
        BOOST_ASSERT(new_uncaugth_count  == m_uncaught_count || new_uncaugth_count == m_uncaught_count + 1);  
        return new_uncaugth_count > m_uncaught_count;  
    }  
```  
  
This will help detect situations (even if not all) when `excepiton checker` is used in a coroutine or some other suspendable task.

---

## Comment 1

> Username: Lastique  
> Created at: 2023-04-23 21:42:58 UTC  
> Updated at: 2023-04-23 21:44:25 UTC  
> Url: https://github.com/boostorg/scope/issues/2#issuecomment-1519175331  

Thanks for the suggestion, although I doubt this will be very useful. I think, there is plenty indication that this tool is incompatible with coroutines.
