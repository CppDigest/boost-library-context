# #244 fix typos [Merged]

> Username: hia3  
> Created at: 2017-09-29 15:12:40 UTC  
> Updated at: 2021-10-02 22:08:32 UTC  
> Merged at: 2017-09-30 17:26:29 UTC  
> Closed at: 2017-09-30 17:26:29 UTC  
> Url: https://github.com/boostorg/build/pull/244  

I hope "ass needed" is not another idiom/abbreviation like "iff" for example.

---

## Review 1 [Commented]

> Username: eldiener  
> Created_at: 2017-09-30 14:16:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/244#pullrequestreview-66314127  

📁 doc/src/faq.xml

```diff
 435 |       and it is possible to later make <code>my-lib</code> into a regular
 436 |       compiled library without having to that its dependants' declarations.
 437 |+       <!-- Even if "without having to that its" is correct English, I'd suggest to simplify this sentence. -->
```

> Username: eldiener  
> Created_at: 2017-09-30 14:16:51 UTC  
> Updated_at: 2017-09-30 15:05:09 UTC  
> Url: https://github.com/boostorg/build/pull/244#discussion_r142007012  

I think the correction should be:  
"without having to add that to its dependants' declarations."  
Even in this correction the word 'that' is still a bit ambiguous, so maybe a better correction would be:  
"without having to add the includes to its dependants' declarations."


---
