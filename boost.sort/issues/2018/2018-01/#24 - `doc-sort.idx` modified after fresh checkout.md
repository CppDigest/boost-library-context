# #24 - `doc/sort.idx` modified after fresh checkout [Closed]

> Username: pdimov  
> Created at: 2018-01-18 19:03:30 UTC  
> Updated at: 2023-01-29 00:51:53 UTC  
> Closed at: 2023-01-29 00:51:52 UTC  
> Url: https://github.com/boostorg/sort/issues/24  

After a fresh checkout on Linux, `doc/sort.idx` appears modified and there's no way to un-modify it. This could be caused by the file having Windows line endings in the repository.

---

## Comment 1

> Username: spreadsort  
> Created at: 2023-01-29 00:51:52 UTC  
> Url: https://github.com/boostorg/sort/issues/24#issuecomment-1407525125  

I think this fixed it: https://github.com/boostorg/sort/commit/ee0bf8bb74b1f13847517bc0fc3d71934ec86b20
