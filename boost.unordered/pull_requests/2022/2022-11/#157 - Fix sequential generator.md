# #157 Fix sequential generator [Merged]

> Username: cmazakas  
> Created at: 2022-11-01 17:19:56 UTC  
> Updated at: 2022-11-02 20:14:28 UTC  
> Merged at: 2022-11-02 18:22:17 UTC  
> Closed at: 2022-11-02 18:22:17 UTC  
> Url: https://github.com/boostorg/unordered/pull/157  

* `g` isn't `origin` so don't compare it to `INT_MAX`  
* `origin + 1` can potentially overflow so we remove it  
* opt into more idiomatic usage `BOOST_ASSERT_MSG()`

---
