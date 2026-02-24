# #665 - value_ref::is_builtin [Closed]

> Username: dimarusyy  
> Created at: 2022-01-05 11:01:50 UTC  
> Updated at: 2022-01-16 17:23:00 UTC  
> Closed at: 2022-01-16 17:23:00 UTC  
> Url: https://github.com/boostorg/json/issues/665  

Is `value_ref::is_builtin` necessary ?  
I don't see it's been used anywhere in the code.

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-01-05 13:31:20 UTC  
> Url: https://github.com/boostorg/json/issues/665#issuecomment-1005687433  

Indeed, it's not used by the library. If you make a PR to remove it, I'll merge it.

---

## Comment 2

> Username: grisumbras  
> Created at: 2022-01-16 17:23:00 UTC  
> Url: https://github.com/boostorg/json/issues/665#issuecomment-1013916978  

Fixed via bb81c7b3bb823d6c3c99b12327165acb3838c923
