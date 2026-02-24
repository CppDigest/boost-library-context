# #868 Version 137 [Merged]

> Username: vinniefalco  
> Created at: 2017-11-04 02:48:11 UTC  
> Updated at: 2017-11-12 22:43:26 UTC  
> Merged at: 2017-11-04 16:59:45 UTC  
> Closed at: 2017-11-04 16:59:45 UTC  
> Url: https://github.com/boostorg/beast/pull/868  

* ConstBufferSequence mandates pointer equivalence  
* Add FieldsWriter constructor requirement  
* Tidy up some documented constructor syntax

---

## Review 1 [Commented]

> Username: djarek  
> Created_at: 2017-11-04 07:52:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/868#pullrequestreview-74249376  

📁 doc/qbk/07_concepts/FieldsWriter.qbk

```diff
  60 |     ]
  61 | ][
  62 |+     [`W{f}`]
```

> Username: djarek  
> Created_at: 2017-11-04 07:52:33 UTC  
> Url: https://github.com/boostorg/beast/pull/868#discussion_r148925246  

Isn't this an optional requirement (only used if you use chunk-encoding)?

> Username: vinniefalco  
> Created_at: 2017-11-04 08:44:59 UTC  
> Url: https://github.com/boostorg/beast/pull/868#discussion_r148925819  

Kind of. It has to be in the documentation or else user-defined FIelds can't be used as trailers. Obviously, this concept needs work. But there is no time to do it for this release. So for now, I just list this as a requirement. Although me and you both know it is not.


---
