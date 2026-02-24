# #246 - grammar::range support reference, value_type [Closed]

> Username: vinniefalco  
> Created at: 2022-07-26 01:08:02 UTC  
> Updated at: 2022-07-31 03:21:00 UTC  
> Closed at: 2022-07-31 03:21:00 UTC  
> Url: https://github.com/boostorg/url/issues/246  

`grammar::range` iterators should return `reference` and have an template parameter option to customize the `value_type` (or default it to be the same as `reference`). The iterators should return const reference if `reference` is not a builtin.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-07-26 01:08:46 UTC  
> Url: https://github.com/boostorg/url/issues/246#issuecomment-1194852138  

The range `query_rule::value_type` should use `query_param_view` for reference and `query_param` for value type.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-07-31 03:21:00 UTC  
> Url: https://github.com/boostorg/url/issues/246#issuecomment-1200339654  

Hmm.. the second part is done but the first part I think we will just pass on it as "won't implement."
