# #132 - span breaks range (msvc, 2017) [Closed]

> Username: timblechmann  
> Created at: 2022-02-08 08:02:12 UTC  
> Updated at: 2022-05-14 01:03:08 UTC  
> Closed at: 2022-05-14 01:03:08 UTC  
> Url: https://github.com/boostorg/range/issues/132  

not sure if it's related to span or range: https://github.com/boostorg/core/issues/105

---

## Comment 1

> Username: glenfe  
> Created at: 2022-05-14 01:03:08 UTC  
> Url: https://github.com/boostorg/range/issues/132#issuecomment-1126598761  

The defect is in MSVC (see https://godbolt.org/z/44PK5Gxnf) but we have a fix in Core in develop. Will be in 1.80.0.
