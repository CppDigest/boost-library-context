# #554 - X3 does not have a permutation operator (^) [Open]

> Username: rubenvb  
> Created at: 2019-11-22 19:16:24 UTC  
> Updated at: 2025-05-10 14:01:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/554  

It performs a specific function (any of, in any order, each one a single time).  
  
It existed in Qi, but is absent from X3.  
I don't think there's an easy way to implement it generically building on the other possibilities of X3.

---

## Comment 1

> Username: djowel  
> Created at: 2019-11-23 00:22:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/554#issuecomment-557741858  

There is no plan to support it in X3.

---

## Comment 2

> Username: saki7  
> Created at: 2025-05-10 01:23:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/554#issuecomment-2868196487  

I think we can theoretically have it in X3. (unless the template won't get instantiated infinitely.)  
  
While I don't have immediate demand for such feature, I'm fond of the concept of permutation and combination in general. That's going to be a good challenge right?

---

## Comment 3

> Username: cppljevans  
> Created at: 2025-05-10 14:01:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/554#issuecomment-2868897259  

I believe the following is a workaround in x3:  
  
  https://stackoverflow.com/posts/77856443/revisions
