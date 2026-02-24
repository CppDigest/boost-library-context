# #275 - rule for alternatives for the same value type [Closed]

> Username: vinniefalco  
> Created at: 2022-07-29 16:48:39 UTC  
> Updated at: 2022-09-14 00:41:19 UTC  
> Closed at: 2022-09-14 00:41:19 UTC  
> Url: https://github.com/boostorg/url/issues/275  

We could use a non-terminal rule that returns `result<T>` for one or more rules, which are required to have the same `using value_type = T`  
  
assert on `mp_same<...>`, the use case is `uri_reference_rule`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-14 00:41:19 UTC  
> Url: https://github.com/boostorg/url/issues/275#issuecomment-1246092556  

The hell with this
