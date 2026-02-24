# #2112 - Examples use executor_type [Open]

> Username: vinniefalco  
> Created at: 2020-10-23 22:18:05 UTC  
> Updated at: 2022-09-24 05:05:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2112  

The examples have `net::io_context&` parameters (e.g. `listener`). They should be passing executors instead, e.g. using a glocal `executor_type` alias.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:05:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2112#issuecomment-1256862055  

Should they? Isn't both perfectly legal?
