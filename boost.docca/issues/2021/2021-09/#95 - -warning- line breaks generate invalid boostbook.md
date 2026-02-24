# #95 - "warning: line breaks generate invalid boostbook" [Closed]

> Username: vinniefalco  
> Created at: 2021-09-22 14:29:36 UTC  
> Updated at: 2022-08-05 15:01:38 UTC  
> Closed at: 2022-08-05 15:01:38 UTC  
> Url: https://github.com/boostorg/docca/issues/95  

Boostbook generates this warning  
> warning: line breaks generate invalid boostbook  
  
from this xslt output:  
  
```  
    [Constructor. [br][role silver —][br]Construct from raw bytes. [br][role silver —][br]Construct from an unsigned integer.   
```  
  
I think its the `[br]`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-05 01:54:19 UTC  
> Url: https://github.com/boostorg/docca/issues/95#issuecomment-1205952953  

I am still getting this

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-08-05 05:12:29 UTC  
> Url: https://github.com/boostorg/docca/issues/95#issuecomment-1206049738  

We could just comment it out in the source file:  
https://github.com/boostorg/quickbook/blob/5f2e8c24cb813b4b0b94d3316e664b971c5b71dd/src/actions.cpp#L265

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-08-05 15:01:37 UTC  
> Url: https://github.com/boostorg/docca/issues/95#issuecomment-1206556251  

the fix worked!  
![image](https://user-images.githubusercontent.com/1503976/183104975-33d44c11-a2a0-45f1-981d-6ca432c8e75e.png)
