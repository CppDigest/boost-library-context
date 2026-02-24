# #5 - static_pool should have a reference count [Closed]

> Username: vinniefalco  
> Created at: 2020-01-22 04:08:59 UTC  
> Updated at: 2021-09-30 15:02:21 UTC  
> Closed at: 2021-09-30 15:02:21 UTC  
> Url: https://github.com/boostorg/url/issues/5  

When the last allocation is freed in the `static_pool`, all the memory should reset and be available again.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-09-30 15:02:21 UTC  
> Url: https://github.com/boostorg/url/issues/5#issuecomment-931404274  

`static_pool` has been upgraded
