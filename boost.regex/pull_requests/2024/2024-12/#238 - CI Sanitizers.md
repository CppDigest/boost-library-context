# #238 CI Sanitizers [Merged]

> Username: cmazakas  
> Created at: 2024-12-19 17:39:57 UTC  
> Updated at: 2024-12-31 15:32:30 UTC  
> Merged at: 2024-12-31 10:57:26 UTC  
> Closed at: 2024-12-31 10:57:26 UTC  
> Url: https://github.com/boostorg/regex/pull/238  

@jzmaddock I closed my old PR and opened up this new one because rebasing was a little too messy.  
  
This PR updates the CI to use ubsan and asan for the latest versions of gcc and clang on Linux. This PR also includes a couple of fixes flagged by the sanitizers.

---

## Comment 1

> Username: cmazakas  
> Created_at: 2024-12-30 20:03:22 UTC  
> Url: https://github.com/boostorg/regex/pull/238#issuecomment-2565870206  

@jzmaddock any thoughts on this PR? The goal was to simply get Regex clean under:  
```bash  
./b2 libs/regex/text address-sanitizer=norecover undefined-sanitizer=norecover variant=debug,release  
```  
  
We need to test both debug and release builds because optimizations mixed with instrumentation catches more things.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-12-31 10:57:22 UTC  
> Url: https://github.com/boostorg/regex/pull/238#issuecomment-2566345992  

Sorry, yes, that looks fine, merging...

---
