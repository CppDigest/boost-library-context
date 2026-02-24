# #117 - Correct method of creating functions that take safe objects with trap policies [Open]

> Username: matoro  
> Created at: 2021-10-25 14:42:38 UTC  
> Updated at: 2021-10-25 14:42:38 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/117  

Posting this as an issue at Mr. Ramey's request.  
  
It appears that there is no way to create a function which can accept a safe object with any execution policy, as a call to such a function instantiated with a trap policy will always fail to compile.  It is however possible to call a function with a runtime exception policy, and this does work as intended.   I gathered that this is because the "possible values" is defined solely by the range properties of the object, and not from actual information known by the compiler because it still does not work even with constexpr initialization.  
  
So I expected that the solution to this is to use the safe_literal utilities. However, I don't seem to be able to pass those into functions which accept a safe object because template substitution fails because a safe_literal_impl is not derived from a safe_base.  
  
Example:  
  
```  
template <typename T, typename PP, typename EP>   
boost::safe_numerics::safe<T, PP, EP> addition(boost::safe_numerics::safe<T, PP, EP> a, boost::safe_numerics::safe<T, PP, EP> b)    
{     
    return a + b;    
}     
  
int main(void)   
{     
    using num = boost::safe_numerics::safe<int, boost::safe_numerics::native, boost::safe_numerics::loose_trap_policy>;   
  
    addition(num{ 2 }, num{ 3 }); // this traps   
  
    constexpr auto a = num{ 2 }, b = num{ 3 };    
    addition(a, b); // also traps   
  
    #define lit(x) make_safe_literal(x, boost::safe_numerics::native, boost::safe_numerics::loose_trap_policy)   
    addition(lit(2), lit(3)); // template substitution failure:   
    // 'safe_literal_impl<...>' is not derived from 'safe_base<...>'   
  
    addition(num{ lit(2) }, num{ lit(3) }); //this also traps   
  
    return 0;    
}  
```  
  
Any ideas on how this ought to be implemented?
