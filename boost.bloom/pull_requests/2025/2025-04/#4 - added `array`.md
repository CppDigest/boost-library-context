# #4 added `array` [Merged]

> Username: joaquintides  
> Created at: 2025-04-07 09:42:12 UTC  
> Updated at: 2025-04-08 14:49:21 UTC  
> Merged at: 2025-04-08 14:47:59 UTC  
> Closed at: 2025-04-08 14:47:59 UTC  
> Url: https://github.com/boostorg/bloom/pull/4  

The envisioned primary use case for this is filter serialization.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-04-07 09:51:19 UTC  
> Url: https://github.com/boostorg/bloom/pull/4#issuecomment-2782749705  

An automated preview of the documentation is available at [https://4.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html](https://4.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html)

---

## Comment 2

> Username: pdimov  
> Created_at: 2025-04-07 11:37:40 UTC  
> Url: https://github.com/boostorg/bloom/pull/4#issuecomment-2783026450  

In 2025 this should probably return a `span` of some persuasion, esp. when the array bound isn't the normal `size()`.

---

## Comment 3

> Username: joaquintides  
> Created_at: 2025-04-07 18:41:54 UTC  
> Url: https://github.com/boostorg/bloom/pull/4#issuecomment-2784239736  

> In 2025 this should probably return a `span` of some persuasion, esp. when the array bound isn't the normal `size()`.  
  
Replaced `data` with `span`-returning `array`. Now we can bikeshed about whether `array` is the right name for this operation.

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2025-04-07 18:46:20 UTC  
> Url: https://github.com/boostorg/bloom/pull/4#issuecomment-2784250024  

An automated preview of the documentation is available at [https://4.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html](https://4.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html)

---

## Comment 5

> Username: pdimov  
> Created_at: 2025-04-07 18:52:33 UTC  
> Url: https://github.com/boostorg/bloom/pull/4#issuecomment-2784264683  

Sounds OK to me.

---

## Review 6 [Commented]

> Username: cmazakas  
> Created_at: 2025-04-07 19:52:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/bloom/pull/4#pullrequestreview-2747958466  

📁 doc/bloom/tutorial.adoc

```diff
 209 |+ member function, which can be leveraged for filter serialization:
 210 |+ 
 211 |+ [listing,subs="+macros,+quotes"]
```

> Username: cmazakas  
> Created_at: 2025-04-07 19:52:33 UTC  
> Updated_at: 2025-04-07 19:52:34 UTC  
> Url: https://github.com/boostorg/bloom/pull/4#discussion_r2031916900  

Unless I'm recalling incorrectly, we should only need this when we're creating code blocks with cross-references, which I don't think we're using here.  
  
This might avoid the need for the `pass:[]` macro invocation.


---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2025-04-08 07:51:22 UTC  
> Url: https://github.com/boostorg/bloom/pull/4#issuecomment-2785555021  

An automated preview of the documentation is available at [https://4.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html](https://4.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2025-04-08 09:26:26 UTC  
> Url: https://github.com/boostorg/bloom/pull/4#issuecomment-2785808919  

An automated preview of the documentation is available at [https://4.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html](https://4.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html)

---
