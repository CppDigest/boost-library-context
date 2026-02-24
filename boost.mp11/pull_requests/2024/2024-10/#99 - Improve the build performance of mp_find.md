# #99 Improve the build performance of mp_find. [Closed]

> Username: Mizuchi  
> Created at: 2024-10-25 07:54:45 UTC  
> Updated at: 2024-10-25 11:59:38 UTC  
> Closed at: 2024-10-25 11:59:37 UTC  
> Url: https://github.com/boostorg/mp11/pull/99  

This improves the build time by looking up 100 elements in a batch.  
  
Note this is just an optimization. It won't change any observable behavior. In addition, this is disabled for GCC due to a [gcc bug](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=85282).  
  
Tested by creating a list with 996 elements and call mp_find: https://gist.github.com/Mizuchi/fce64eaa400288de136be1181f0edc96  
  
The mp_find_2.cpp build time reduced from 400ms to 80ms.  
  
It's possible to re-implement this with boost.preprocessor to make the batch size (100) configurable, but that will introduce boost.preprocessor as a dependency.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-10-25 09:14:45 UTC  
> Url: https://github.com/boostorg/mp11/pull/99#issuecomment-2437293590  

I don't think that's going to work if the list contains duplicates.

---

## Comment 2

> Username: Mizuchi  
> Created_at: 2024-10-25 11:59:38 UTC  
> Url: https://github.com/boostorg/mp11/pull/99#issuecomment-2437597079  

> I don't think that's going to work if the list contains duplicates.  
  
Good point. It's fixable by (ab)using partial specialization order (I created a new commit for demo).  
  
This can still reduce the build-time from 400ms to 140ms (for the mp_find_2.cpp example), though that makes the implementation much more complex.  
  
Let me think about whether there is a better way to do. I'll close this pull request for now.

---
