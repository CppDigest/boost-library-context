# #363 - value_to behavior for reference types [Closed]

> Username: sdkrystian  
> Created at: 2020-09-18 19:45:05 UTC  
> Updated at: 2020-09-25 18:42:25 UTC  
> Closed at: 2020-09-25 18:42:25 UTC  
> Url: https://github.com/boostorg/json/issues/363  

Right now we error if `T` is a reference type

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-09-25 17:12:39 UTC  
> Url: https://github.com/boostorg/json/issues/363#issuecomment-699048093  

@vinniefalco Is this actionable?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-25 18:28:53 UTC  
> Url: https://github.com/boostorg/json/issues/363#issuecomment-699085091  

I think we are supposed to error, we should just make sure that the constraints and/or static assertions are the way that we want them. I think this is more about double checking the SFINAE expressions to make sure they are right.

---

## Comment 3

> Username: sdkrystian  
> Created at: 2020-09-25 18:39:57 UTC  
> Url: https://github.com/boostorg/json/issues/363#issuecomment-699090211  

We already do error

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-09-25 18:42:25 UTC  
> Url: https://github.com/boostorg/json/issues/363#issuecomment-699091338  

Nothing actionable then.
