# #147 - function_traits doesn't support qualified function types [Open]

> Username: pdimov  
> Created at: 2020-09-12 16:01:31 UTC  
> Updated at: 2020-09-12 16:01:31 UTC  
> Url: https://github.com/boostorg/type_traits/issues/147  

Add the moment `function_traits` works for `void () noexcept`, but not for `void () const` or `void () &`.  
  
If we fix that, it would be a good opportunity to also add  
```  
{  
    template<class...> struct list {};  
    using arguments = list<A1, A2, ..., An>;  
}  
```  
(on C++11) which can be further manipulated with Mp11. (The `argN_type` typedefs aren't generic.)
