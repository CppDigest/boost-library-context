# #20 - Outdated documentation in Boost [Closed]

> Username: FrogTheFrog  
> Created at: 2024-07-03 18:34:35 UTC  
> Updated at: 2024-07-03 20:19:06 UTC  
> Closed at: 2024-07-03 20:18:42 UTC  
> Url: https://github.com/boostorg/scope/issues/20  

Heyo,  
  
the documentation in Boost is outdated (or was never correct) https://www.boost.org/doc/libs/1_85_0/libs/scope/doc/html/scope/scope_guards.html#scope.scope_guards.runtime_defined.  
  
The following example uses deleted move constructor:  
```  
using cleanup_func_t = std::function< void() >;  
// Create an inactive scope guard first, since the cleanup function is not set yet  
boost::scope::scope_exit< cleanup_func_t > cleanup(cleanup_func_t(), false);  
  
// Later in the program, initialize the scope guard with the function selected at run time  
if (cond)  
{  
    cleanup = boost::scope::scope_exit< cleanup_func_t >([]  
    {  
        std::cout << "cond is true" << std::endl;  
    });  
}  
else  
{  
    cleanup = boost::scope::scope_exit< cleanup_func_t >([]  
    {  
        std::cout << "cond is false" << std::endl;  
    });  
}  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2024-07-03 20:19:05 UTC  
> Url: https://github.com/boostorg/scope/issues/20#issuecomment-2207173375  

Thanks for the report, should be fixed now.
