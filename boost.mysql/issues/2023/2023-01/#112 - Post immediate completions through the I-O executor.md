# #112 - Post immediate completions through the I/O executor [Closed]

> Username: anarthal  
> Created at: 2023-01-28 13:04:06 UTC  
> Updated at: 2023-06-24 21:54:54 UTC  
> Closed at: 2023-06-24 21:54:53 UTC  
> Url: https://github.com/boostorg/mysql/issues/112  

As per https://cppalliance.org/boost-release/2022/11/16/KlemensBoost181.html immediate completions should post through the I/O executor, then dispatch to the token's executor. We currently post to the token's executor.

---

## Comment 1

> Username: anarthal  
> Created at: 2023-04-01 13:43:17 UTC  
> Url: https://github.com/boostorg/mysql/issues/112#issuecomment-1492973807  

With Boost 1.82, that should be `dispatch(get_associated_immediate_executor(h), std::move(self))`
