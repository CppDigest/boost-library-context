# #89 - Named pipe support [Open]

> Username: anarthal  
> Created at: 2022-05-18 22:01:51 UTC  
> Updated at: 2024-08-10 16:20:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/89  

Windows named pipes should already work with the current implementation. However, we should provide the following:  
  
* Helper typedefs, as in `tcp_connection` and `unix_connection`.  
* Testing coverage.  
* Docs  
* Supporting the `connect` and `close` methods for named pipes.

---

## Comment 1

> Username: anarthal  
> Created at: 2024-08-10 16:20:14 UTC  
> Url: https://github.com/boostorg/mysql/issues/89#issuecomment-2282199390  

`any_connection` requires explicit support, and I think this could be worth it.
