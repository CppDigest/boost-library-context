# #114 Recursive iterator advancing [Closed]

> Username: xRodney  
> Created at: 2019-07-16 10:06:02 UTC  
> Updated at: 2019-07-31 16:07:32 UTC  
> Closed at: 2019-07-31 16:07:32 UTC  
> Url: https://github.com/boostorg/filesystem/pull/114  

This PR fixes issues #112 and #113, so that the iterator does not get stuck in an infinite loop and does not violate any assertions.  
  
I have taken the "implicit no_push" approach, so that the problematic directory may be returned twice from the iterator.

---

## Comment 1

> Username: Lastique  
> Created_at: 2019-07-31 16:07:32 UTC  
> Url: https://github.com/boostorg/filesystem/pull/114#issuecomment-516915665  

> I have taken the "implicit no_push" approach, so that the problematic directory may be returned twice from the iterator.  
  
Sorry, but that is not an acceptable side effect. See my comment https://github.com/boostorg/filesystem/issues/113#issuecomment-516910150.

---
