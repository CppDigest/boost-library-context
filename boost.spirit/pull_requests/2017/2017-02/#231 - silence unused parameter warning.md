# #231 silence unused parameter warning [Merged]

> Username: bebuch  
> Created at: 2017-02-10 11:28:39 UTC  
> Updated at: 2017-02-24 11:10:43 UTC  
> Merged at: 2017-02-24 11:10:43 UTC  
> Closed at: 2017-02-24 11:10:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/231  



---

## Comment 1

> Username: djowel  
> Created_at: 2017-02-24 01:40:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/231#issuecomment-282178343  

I prefer (Parser const& /*p*/) or simply (Parser const&)

---

## Comment 2

> Username: bebuch  
> Created_at: 2017-02-24 09:47:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/231#issuecomment-282249383  

The parameter is used, but only in debug mode via #ifdef a few lines below. ;-)

---
