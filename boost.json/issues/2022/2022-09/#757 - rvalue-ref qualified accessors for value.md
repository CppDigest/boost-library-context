# #757 - rvalue-ref qualified accessors for value [Closed]

> Username: grisumbras  
> Created at: 2022-09-26 10:40:18 UTC  
> Updated at: 2022-10-24 14:34:43 UTC  
> Closed at: 2022-10-24 14:34:43 UTC  
> Url: https://github.com/boostorg/json/issues/757  

`as_string` et al. should have rvalue-ref qualified overload.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-26 18:16:24 UTC  
> Url: https://github.com/boostorg/json/issues/757#issuecomment-1258428579  

What exactly does this do?

---

## Comment 2

> Username: grisumbras  
> Created at: 2022-09-27 05:17:20 UTC  
> Url: https://github.com/boostorg/json/issues/757#issuecomment-1258989419  

```c++  
value foo();  
void bar(string)  
  
bar(foo().as_string());  
```  
This will copy the string. With an rvalue-ref overload it wouldn't.
