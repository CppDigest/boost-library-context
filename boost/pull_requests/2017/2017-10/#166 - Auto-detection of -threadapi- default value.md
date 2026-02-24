# #166 Auto-detection of <threadapi> default value [Merged]

> Username: karzhenkov  
> Created at: 2017-10-07 15:03:46 UTC  
> Updated at: 2017-10-08 14:59:55 UTC  
> Merged at: 2017-10-07 16:20:54 UTC  
> Closed at: 2017-10-07 16:20:54 UTC  
> Url: https://github.com/boostorg/boost/pull/166  

`<threadapi>` default value should be deduced from `<target-os>`. The logic is implemented in boostorg/build#251. The actual value of `<threadapi>` (taking into account build request) should be evaluated via `<conditional>` in the top-level project requirements. See also discussion [here](https://github.com/boostorg/thread/commit/e9ce83b3998a0de2a825629531c40aab55b9747c).

---
