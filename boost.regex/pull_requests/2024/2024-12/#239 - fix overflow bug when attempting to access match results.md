# #239 fix overflow bug when attempting to access match results [Merged]

> Username: cmazakas  
> Created at: 2024-12-20 19:33:17 UTC  
> Updated at: 2024-12-22 20:26:23 UTC  
> Merged at: 2024-12-21 09:44:24 UTC  
> Closed at: 2024-12-21 09:44:24 UTC  
> Url: https://github.com/boostorg/regex/pull/239  

ubsan raises an error here on integer overflow when the format string includes a number that's set to INT_MAX. This passively fixes another asan failure caught by fuzzing.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2024-12-21 09:44:18 UTC  
> Url: https://github.com/boostorg/regex/pull/239#issuecomment-2558067147  

Thanks @cmazakas that looks good to me!

---
