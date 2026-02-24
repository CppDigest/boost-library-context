# #344 - Design Goal [Closed]

> Username: vinniefalco  
> Created at: 2020-09-15 23:23:44 UTC  
> Updated at: 2020-10-06 00:44:27 UTC  
> Closed at: 2020-10-06 00:44:24 UTC  
> Url: https://github.com/boostorg/json/issues/344  

The overview should be explicit about the design goal:  
  
That high-level contract -- as I understand it -- is:  
1. Any json::value that you can build can be serialized and then deserialized, and you are guaranteed that the resulting json::value will be equal to the original.  
2. JSON inputs where number values cannot be represented losslessly in uint64_t, int64_t and double, may render different values when parsed and then serialized back, and for extremely big number values can even fail to parse.  
3. Whatever JSON output you can produce with this library, we guarantee it can be passed by any common JSON implementation (probably also based on uint64_t+int64_t+double implementation.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-10-06 00:44:24 UTC  
> Url: https://github.com/boostorg/json/issues/344#issuecomment-703964195  

This is in the Overview now
