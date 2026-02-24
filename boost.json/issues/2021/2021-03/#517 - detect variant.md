# #517 - detect variant [Closed]

> Username: vinniefalco  
> Created at: 2021-03-03 23:59:27 UTC  
> Updated at: 2022-10-18 15:24:37 UTC  
> Closed at: 2022-10-18 15:24:37 UTC  
> Url: https://github.com/boostorg/json/issues/517  

> std::variant is relatively easy because what else would have .valueless_on_exception(). I suppose it would be accurate enough if you check for v.index(), v.valueless_on_exception() and visit(f, v)

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-05-23 07:11:59 UTC  
> Url: https://github.com/boostorg/json/issues/517#issuecomment-846516158  

Should we represent `variant` as an array with type index being the first element?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-05-25 19:34:02 UTC  
> Url: https://github.com/boostorg/json/issues/517#issuecomment-848205426  

I have no idea
