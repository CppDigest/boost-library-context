# #77 - Consider accepting types in can_invoke [Closed]

> Username: ldionne  
> Created at: 2016-04-19 19:01:45 UTC  
> Updated at: 2016-05-15 22:15:21 UTC  
> Closed at: 2016-05-15 22:15:21 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/77  

In the "Quick example", you say of `can_invoke`  
  
> ``` c++  
> // Note that since can_invoke models std::invoke,  
> // only a value-style function is defined.  
> ```  
  
I find myself wondering why you don't also provide a type-based interface. I could imagine something like  
  
``` c++  
can_invoke<foo, std::nullptr_t>()  
```  
  
instead of   
  
``` c++  
can_invoke(foo{}, nullptr)  
```  
  
In particular, imagine the following use case:  
  
``` c++  
template <typename F, typename ...Args, typename = std::enable_if_t<  
    can_invoke<F, Args...>()  
>>  
auto f(F f, Args ...args) {  
    // invoke f with args  
  
    // maybe do something else  
}  
```  
  
I'm not saying it's a bad decision to provide only the value-based API here, but you'd have to at least document the rationale for breaking the consistency of the rest of the API.

---

## Comment 1

> Username: badair  
> Created at: 2016-05-15 22:15:19 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/77#issuecomment-219314081  

Fixed in 2877891 (documentation still needs updating). Thanks!
