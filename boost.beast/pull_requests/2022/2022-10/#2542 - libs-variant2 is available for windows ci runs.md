# #2542 libs/variant2 is available for windows ci runs. [Merged]

> Username: klemens-morgenstern  
> Created at: 2022-10-17 15:15:51 UTC  
> Updated at: 2022-10-17 16:05:37 UTC  
> Merged at: 2022-10-17 16:05:36 UTC  
> Closed at: 2022-10-17 16:05:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2542  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-17 15:25:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2542#pullrequestreview-1144379585  

📁 tools/get-boost.sh

```diff
  84 |     libs/typeof \
  85 |     libs/unordered \
  86 |+     libs/variant2 \
```

> Username: vinniefalco  
> Created_at: 2022-10-17 15:25:13 UTC  
> Updated_at: 2022-10-17 15:25:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2542#discussion_r997208328  

Did Beast use variant2 before?

> Username: klemens-morgenstern  
> Created_at: 2022-10-17 15:29:38 UTC  
> Url: https://github.com/boostorg/beast/pull/2542#discussion_r997213454  

No, it looks to be a transitive dependency from boost/system from by json example. I am currently searching for where it's included from.

> Username: klemens-morgenstern  
> Created_at: 2022-10-17 15:34:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2542#discussion_r997219467  

Added to json as a dep 3 days ago, so that would be it.

> Username: vinniefalco  
> Created_at: 2022-10-17 16:03:27 UTC  
> Url: https://github.com/boostorg/beast/pull/2542#discussion_r997251236  

Ok


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-10-17 15:43:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2542#issuecomment-1281079179  

![pullrequest](https://2542.beast.tracing.cppalliance.org/pullrequest-aad81932.png)  
Timeline tracing charts: [https://2542.beast.tracing.cppalliance.org/index.html](https://2542.beast.tracing.cppalliance.org/index.html)

---
