# #48 - Arguments forwarding [Closed]

> Username: viccpp  
> Created at: 2017-03-21 13:33:02 UTC  
> Updated at: 2017-03-21 14:50:31 UTC  
> Closed at: 2017-03-21 13:49:23 UTC  
> Url: https://github.com/boostorg/context/issues/48  

Why `calcc()` and `continuation::resume()` pass arguments by value (`Arg ...`) instead of forwarding references (`Arg && ...`)?

---

## Comment 1

> Username: olk  
> Created at: 2017-03-21 13:49:22 UTC  
> Url: https://github.com/boostorg/context/issues/48#issuecomment-288083378  

yes - see implementations of std::packaged_task

---

## Comment 2

> Username: viccpp  
> Created at: 2017-03-21 14:17:43 UTC  
> Url: https://github.com/boostorg/context/issues/48#issuecomment-288091990  

What is the point? `std::thread` uses references, `std::async` uses references. `std::function` also uses values. Isn't it because `Args` are the part of signature which is a template parameter of `std::packaged_task` and `std::function`? `continuation` is not a template.

---

## Comment 3

> Username: olk  
> Created at: 2017-03-21 14:43:16 UTC  
> Updated at: 2017-03-21 14:43:31 UTC  
> Url: https://github.com/boostorg/context/issues/48#issuecomment-288100287  

the type (value,reference) is forwarded to std::tuple

---

## Comment 4

> Username: viccpp  
> Created at: 2017-03-21 14:50:31 UTC  
> Url: https://github.com/boostorg/context/issues/48#issuecomment-288102746  

Get it, ty
