# #6 - Passing a function reference to a scope guard factory fails to compile [Closed]

> Username: Lastique  
> Created at: 2023-11-26 19:47:30 UTC  
> Updated at: 2023-11-27 22:50:33 UTC  
> Closed at: 2023-11-27 22:50:33 UTC  
> Url: https://github.com/boostorg/scope/issues/6  

Reported on the boost-dev ML.  
  
Given  
  
```  
void f();  
  
int main()  
{  
    auto a = boost::scope::make_scope_fail(f);  
    boost::scope::scope_fail b(f);  
}  
```  
  
Only the declaration of `b` compiles. The one of `a` fails. I think the one of `a` should compile, too. Something like `std::decay` instead of `typename std::remove_cv< typename std::remove_reference< F >::type >::type` at https://github.com/Lastique/scope/blob/develop/include/boost/scope/scope_exit.hpp#L535
