# #68 - lazy - should support any Callable [Closed]

> Username: viboes  
> Created at: 2015-10-04 16:27:00 UTC  
> Updated at: 2015-10-30 23:16:22 UTC  
> Closed at: 2015-10-30 23:16:22 UTC  
> Url: https://github.com/boostorg/hof/issues/68  

What is the real functionality of this function other than store the passed function and been able to call it later on?

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 07:10:11 UTC  
> Url: https://github.com/boostorg/hof/issues/68#issuecomment-145446668  

For the same reason you would use lambda expression(or `std::bind`), except `fit::lazy` is constexpr friendly. Also, `std::mem_fn` can be used for pointer to member functions.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-05 22:03:51 UTC  
> Url: https://github.com/boostorg/hof/issues/68#issuecomment-145682015  

Yes, but binding a function with no arguments doesn't add nothing and  
  
``` c++  
[](auto a, auto b) { return f(a,b)  
```  
  
doesn't add nothing to `f`.  
  
I'm missing something trivial and I don't know what is it.

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-25 23:36:44 UTC  
> Url: https://github.com/boostorg/hof/issues/68#issuecomment-150987675  

> Yes, but binding a function with no arguments doesn't add nothing  
  
Where is this happening?

---

## Comment 4

> Username: pfultz2  
> Created at: 2015-10-30 23:16:22 UTC  
> Url: https://github.com/boostorg/hof/issues/68#issuecomment-152671813  

Closing this since I haven't heard anything back about this.
