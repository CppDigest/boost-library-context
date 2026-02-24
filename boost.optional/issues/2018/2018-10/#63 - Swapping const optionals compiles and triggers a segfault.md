# #63 - Swapping const optionals compiles and triggers a segfault [Closed]

> Username: akrzemi1  
> Created at: 2018-10-24 08:02:01 UTC  
> Updated at: 2018-10-29 20:19:05 UTC  
> Closed at: 2018-10-29 20:19:05 UTC  
> Url: https://github.com/boostorg/optional/issues/63  

The following code compiles and triggers a segfault at run-time.   
  
```c++  
const boost::optional<int> co1{17};  
const boost::optional<int> co2{666};  
swap(co1, co2);  
```  
  
This would be prevented by a `swap` overload taking const optionals.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2018-10-24 08:41:05 UTC  
> Url: https://github.com/boostorg/optional/issues/63#issuecomment-432566396  

This is a problem with `boost::swap`: https://github.com/boostorg/core/issues/43.
