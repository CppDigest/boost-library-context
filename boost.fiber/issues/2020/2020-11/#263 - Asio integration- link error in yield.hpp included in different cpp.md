# #263 - Asio integration: link error in yield.hpp included in different cpp [Open]

> Username: malirod  
> Created at: 2020-11-04 18:10:20 UTC  
> Updated at: 2020-12-29 07:38:46 UTC  
> Url: https://github.com/boostorg/fiber/issues/263  

In the file `examples/asio/yield.hpp` there is the line  
```  
thread_local yield_t yield{};  
```  
So if this yield.hpp included in two different cpp files then there will be link issue.  
  
If existing code will be changed with following then issue is solved.  
```  
inline yield_t& this_yield() {  
  thread_local yield_t yield;  
  return yield;  
}  
```

---

## Comment 1

> Username: bitbugprime  
> Created at: 2020-12-29 07:38:46 UTC  
> Url: https://github.com/boostorg/fiber/issues/263#issuecomment-751982286  

If you have C++17 it would be simpler to declare the global variable itself inline.
