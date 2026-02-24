# #161 - Add a macro to reduce boilerplate with the factory pattern [Open]

> Username: pfultz2  
> Created at: 2016-04-12 17:50:27 UTC  
> Updated at: 2016-04-12 17:52:31 UTC  
> Url: https://github.com/boostorg/hof/issues/161  

An alternative to declaring functions using the `FIT_STATIC_LAMBDA_FUNCTION` is to use the factory pattern in C++14:  
  
``` cpp  
struct lambda_factor  
{  
   auto operator*() const  
   {  
      return [] { ... };  
   }  
};  
  
FIT_STATIC_FUNCTION(lambda) = fit::indirect(lambda_factor{});  
```  
  
It might be possible to have a macro to reduce this boilerplate:  
  
``` cpp  
#define FIT_STATIC_FACTORY(name) \  
struct name ## _factory { auto operator*() const; } \  
FIT_STATIC_FUNCTION(name) = fit::indirect(name ## _factory{}); \  
auto name ## _factory::operator*() const  
```  
  
Then it could be written as:  
  
``` cpp  
FIT_STATIC_FACTORY(lambda)  
{  
    return [] { ... };  
}  
```  
  
By making it a macro it could fallback on using a lambda in C++11, although this would require a semicolon at the end.
