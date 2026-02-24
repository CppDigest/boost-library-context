# #1636 - Http::Verb eq Missing [Closed]

> Username: FBaumeister  
> Created at: 2019-06-11 13:28:34 UTC  
> Updated at: 2019-06-12 04:24:18 UTC  
> Closed at: 2019-06-12 04:24:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1636  

Hi,  
I just came across these lines:  
'eq' is not used for comparison of of "CL" and "IND" in Lines 133 and 138, or is this intentional?  
Am I missing the point here? :-)  
  
https://github.com/boostorg/beast/blob/6af4c01e5662926372a7e43f3194fe01846a28dd/include/boost/beast/http/impl/verb.ipp#L133  
https://github.com/boostorg/beast/blob/6af4c01e5662926372a7e43f3194fe01846a28dd/include/boost/beast/http/impl/verb.ipp#L138

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-06-11 16:39:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1636#issuecomment-500924518  

That is odd.. investigating! Thanks

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-06-12 01:32:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1636#issuecomment-501085338  

Verbs must be uppercase, so this is technically not a bug (it works correctly as written). But it looks weird, and preliminary benchmarks suggest using a simple `operator==` performs better.

---

## Comment 3

> Username: FBaumeister  
> Created at: 2019-06-12 04:24:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1636#issuecomment-501115256  

Thank you!
