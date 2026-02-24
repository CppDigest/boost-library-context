# #117 Treat warnings as errors in CI [Merged]

> Username: sdkrystian  
> Created at: 2020-06-29 21:58:39 UTC  
> Updated at: 2020-07-01 03:12:56 UTC  
> Merged at: 2020-06-30 01:15:18 UTC  
> Closed at: 2020-06-30 01:15:18 UTC  
> Url: https://github.com/boostorg/json/pull/117  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-29 23:48:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/117#pullrequestreview-439575843  

📁 test/snippets.cpp

```diff
  80 |         json::string jstr1 = "helloworld";
  81 |         json::string jstr2 = "world";
```

> Username: vinniefalco  
> Created_at: 2020-06-29 23:48:44 UTC  
> Updated_at: 2020-06-29 23:48:45 UTC  
> Url: https://github.com/boostorg/json/pull/117#discussion_r447322744  

This would be a better example if the two strings did not share the word "world"


---
