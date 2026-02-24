# #193 - deferred doesn't work for connection in C++11 [Closed]

> Username: anarthal  
> Created at: 2023-12-19 15:35:34 UTC  
> Updated at: 2024-01-14 13:14:28 UTC  
> Closed at: 2024-01-14 13:14:28 UTC  
> Url: https://github.com/boostorg/mysql/issues/193  

`deferred` tokens don't compile under C++11 for `connection`. This has been already fixed in `any_connection`, backport it to `connection`.

---

## Comment 1

> Username: anarthal  
> Created at: 2023-12-26 17:02:11 UTC  
> Url: https://github.com/boostorg/mysql/issues/193#issuecomment-1869665970  

Note that `connection::async_connect` is still incompatible with `deferred` in C++11 due to the dependence on `typename Stream::endpoint_type`. I don't see a way to workaround this. Note that `any_connection::async_connect` doesn't suffer from this.
