# #45 - Unit test: OK packet for a resultset containing rows [Closed]

> Username: anarthal  
> Created at: 2021-02-22 19:33:09 UTC  
> Updated at: 2022-11-28 07:54:56 UTC  
> Closed at: 2022-11-28 07:54:56 UTC  
> Url: https://github.com/boostorg/mysql/issues/45  

Coverage for an issue (already fixed) that would cause UB:  
- Resultset containing rows is read using `fetch_many`/`fetch_all`  
- OK packet contains an `info` string  
- `resultset::info` accessed deallocated memory

---

## Comment 1

> Username: anarthal  
> Created at: 2022-11-28 07:54:56 UTC  
> Url: https://github.com/boostorg/mysql/issues/45#issuecomment-1328673362  

As of d04f2662ba161710f4602f9406884f15e9d3f4b3, the design no longer allows this pitfall.
