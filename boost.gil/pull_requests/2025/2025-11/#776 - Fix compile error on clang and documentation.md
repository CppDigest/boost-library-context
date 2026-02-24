# #776 Fix compile error on clang and documentation [Merged]

> Username: sdebionne  
> Created at: 2025-11-18 09:15:41 UTC  
> Updated at: 2025-11-19 08:20:13 UTC  
> Merged at: 2025-11-19 08:20:13 UTC  
> Closed at: 2025-11-19 08:20:13 UTC  
> Url: https://github.com/boostorg/gil/pull/776  

### Description  
  
Fixes #775   
  
Ports #767 from develop to master ~mloskot  
  
### References  
  
Permission granted to merge this fixup on master for 1.90:  
  
https://lists.boost.org/archives/list/boost@lists.boost.org/message/RVU7AKXUXA6X6K3MG4ZEQZ5WIWVW7I5E/

---

## Comment 1

> Username: sdebionne  
> Created_at: 2025-11-18 09:21:55 UTC  
> Url: https://github.com/boostorg/gil/pull/776#issuecomment-3546406961  

@mloskot Do you know the way to handle "late" commit to `master` when we can't / don't want to merge everything from `develop`?

---

## Comment 2

> Username: mloskot  
> Created_at: 2025-11-18 09:47:17 UTC  
> Url: https://github.com/boostorg/gil/pull/776#issuecomment-3546528978  

@sdebionne   
>  Do you know the way to handle "late" commit to `master` when we can't / don't want to merge everything from `develop`?  
  
I don't know any general Boost policy, but I know some maintainers decided to cherry-pick commits.  
  
I recall this old discussion on the merge policy in https://github.com/boostorg/gil/discussions/663 where we seem to decided we prefer true merges, but we also don't object cherry-picking.

---

## Comment 3

> Username: sdebionne  
> Created_at: 2025-11-18 09:52:36 UTC  
> Url: https://github.com/boostorg/gil/pull/776#issuecomment-3546555881  

Thanks! I did cherry-pick first before creating this PR, hoping to bypass this issue:  
```  
To github.com:boostorg/gil.git  
 ! [remote rejected]     master -> master (protected branch hook declined)  
```  
But no luck. How can I temporarily disable the protection on `master`? I does not look I have the permission for that (unavailable in the repo settings).

---

## Comment 4

> Username: mloskot  
> Created_at: 2025-11-18 09:55:54 UTC  
> Url: https://github.com/boostorg/gil/pull/776#issuecomment-3546574558  

@sdebionne   
> How can I temporarily disable the protection on `master`? I does not look I have the permission for that   
  
Please, try again. You should be able to see it now :)

---

## Comment 5

> Username: sdebionne  
> Created_at: 2025-11-19 07:25:26 UTC  
> Url: https://github.com/boostorg/gil/pull/776#issuecomment-3551184517  

I am  adding the doc fixup as well since we don't need permission for that. The CI is stuck, but I suppose this PR lacks the necessary fixes.  
  
I am planning to fix the CI and merge develop for the next release.

---

## Review 6 [Approved]

> Username: mloskot  
> Created_at: 2025-11-19 08:04:45 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/776#pullrequestreview-3481334890  

Thank you

---
