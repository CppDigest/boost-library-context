# #24 fix travis build [Closed]

> Username: tobias-loew  
> Created at: 2021-05-05 19:13:45 UTC  
> Updated at: 2021-11-16 18:51:44 UTC  
> Closed at: 2021-11-16 18:51:44 UTC  
> Url: https://github.com/boostorg/unordered/pull/24  

pruned source-location from 32-bit C++03 build  
32-bit C++03 build produces a lot of warnings which led to a too big log output and the test failed  
added python 3

---

## Comment 1

> Username: cmazakas  
> Created_at: 2021-11-15 17:51:31 UTC  
> Url: https://github.com/boostorg/unordered/pull/24#issuecomment-969164214  

@tobias-loew Unfortunately, it seems like Travis is largely no longer free so Boost is migrating away from it. We're even aiming to remove the `travis.yml` file in general.

---

## Comment 2

> Username: tobias-loew  
> Created_at: 2021-11-16 18:51:44 UTC  
> Url: https://github.com/boostorg/unordered/pull/24#issuecomment-970571421  

@LeonineKing1199 thanks for the notification. I revoke this PR. Please have a look at #27, where I adopted drone for boost.unordered

---
