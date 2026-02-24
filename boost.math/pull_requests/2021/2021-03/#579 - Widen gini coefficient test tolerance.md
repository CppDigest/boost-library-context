# #579 Widen gini coefficient test tolerance [Merged]

> Username: mborland  
> Created at: 2021-03-22 18:31:21 UTC  
> Updated at: 2021-03-23 18:57:29 UTC  
> Merged at: 2021-03-23 17:05:40 UTC  
> Closed at: 2021-03-23 17:05:40 UTC  
> Url: https://github.com/boostorg/math/pull/579  

Reported in https://github.com/boostorg/math/pull/569 and failure [seen here](https://github.com/boostorg/math/runs/2162144720?check_suite_focus=true#step:12:1493). Increase test tolerance to be match rest of tests with 10*epsilon as acceptable tolerance. Should head off random failures in the future.

---

## Comment 1

> Username: mborland  
> Created_at: 2021-03-23 15:49:10 UTC  
> Url: https://github.com/boostorg/math/pull/579#issuecomment-805014390  

@jzmaddock This should be good to go. The 2x internal failures are both GHA internal errors. The 6x queued tests are all duplicates? Not sure what is going on with the system, but on the actual GHA page it shows the run as complete.

---
