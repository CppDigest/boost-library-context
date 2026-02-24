# #66 Repair custom terminal support [Merged]

> Username: Kojoley  
> Created at: 2018-02-02 13:00:53 UTC  
> Updated at: 2018-02-03 12:43:21 UTC  
> Merged at: 2018-02-03 04:09:20 UTC  
> Closed at: 2018-02-03 04:09:20 UTC  
> Url: https://github.com/boostorg/phoenix/pull/66  

PR #64 had broken custom terminal support. The phoenix test suite seems does not cover this part, but the fix is enough to make Spirit pass its own tests.  
  
Note: `BOOST_PROTO_BASIC_EXTENDS(Expr, actor<Expr>, phoenix_domain)` does not work out.

---

## Comment 1

> Username: Flast  
> Created_at: 2018-02-02 13:21:55 UTC  
> Url: https://github.com/boostorg/phoenix/pull/66#issuecomment-362584629  

Can you show which spirit test failed?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2018-02-02 13:23:14 UTC  
> Url: https://github.com/boostorg/phoenix/pull/66#issuecomment-362584960  

https://travis-ci.org/boostorg/spirit/builds/336340451

---

## Comment 3

> Username: Flast  
> Created_at: 2018-02-02 13:29:11 UTC  
> Url: https://github.com/boostorg/phoenix/pull/66#issuecomment-362586357  

Ah, I see.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2018-02-03 12:43:21 UTC  
> Url: https://github.com/boostorg/phoenix/pull/66#issuecomment-362803538  

Thanks for the fast merge!

---
