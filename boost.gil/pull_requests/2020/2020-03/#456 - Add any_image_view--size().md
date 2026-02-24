# #456 Add any_image_view::size() [Merged]

> Username: sdebionne  
> Created at: 2020-03-19 11:15:36 UTC  
> Updated at: 2020-10-14 08:14:41 UTC  
> Merged at: 2020-03-20 11:42:11 UTC  
> Closed at: 2020-03-20 11:42:11 UTC  
> Url: https://github.com/boostorg/gil/pull/456  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
<!-- What does this pull request do? -->  
  
Adds `any_image_view::size()` that returns the size of the underlying `image_view`.  
  
```  
size_type        View::size()       const; // total number of elements  
```  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Update doc(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2020-03-19 19:57:31 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/456#pullrequestreview-378048895  

Although I'm not a heavy user of dynamic_image, I don't see this may introduce any breakages or problems. I think it just completes the interface. If no objections arrive soon from @stefanseefeld, @lpranam , @chhenning or anyone else active/interested in GIL, then I'll merge it.  
  
@sdebionne Thank you for the contribution.

---

## Review 2 [Approved]

> Username: lpranam  
> Created_at: 2020-03-19 21:03:07 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/456#pullrequestreview-378091502  

Everything looks good to me :)

---

## Comment 3

> Username: sdebionne  
> Created_at: 2020-03-20 10:28:07 UTC  
> Url: https://github.com/boostorg/gil/pull/456#issuecomment-601628882  

Yeah, it's not really a life-changing PR :-)

---
