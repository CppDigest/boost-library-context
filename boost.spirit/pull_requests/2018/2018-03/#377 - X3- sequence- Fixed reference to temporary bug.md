# #377 X3: sequence: Fixed reference to temporary bug [Merged]

> Username: Kojoley  
> Created at: 2018-03-15 14:37:02 UTC  
> Updated at: 2018-03-16 17:10:41 UTC  
> Merged at: 2018-03-16 17:10:38 UTC  
> Closed at: 2018-03-16 17:10:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/377  



---

## Comment 1

> Username: djowel  
> Created_at: 2018-03-15 21:44:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/377#issuecomment-373532730  

It should be returning the reference to the fusion sequence element. Why is it not doing that?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2018-03-15 21:47:30 UTC  
> Updated_at: 2018-03-15 21:48:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/377#issuecomment-373533466  

And it will if `deref` returns reference for a particular attribute. But it can also return a value (for example `adt_attribute_proxy`), then there will be use after scope.

---

## Comment 3

> Username: djowel  
> Created_at: 2018-03-15 21:50:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/377#issuecomment-373534141  

So we should deal with both cases.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2018-03-15 21:50:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/377#issuecomment-373534308  

And it already. Note that `fusion::result_of::deref` is exact return type of `deref` call.

---

## Comment 5

> Username: djowel  
> Created_at: 2018-03-15 21:51:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/377#issuecomment-373534422  

Oh right!

---

## Comment 6

> Username: djowel  
> Created_at: 2018-03-15 21:52:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/377#issuecomment-373534786  

I see. That add_reference is bogus indeed.

---

## Comment 7

> Username: Kojoley  
> Created_at: 2018-03-15 21:54:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/377#issuecomment-373535136  

> That add_reference is bogus  
  
Yup :-)

---
