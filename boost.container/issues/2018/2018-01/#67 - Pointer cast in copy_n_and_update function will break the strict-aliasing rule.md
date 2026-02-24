# #67 - Pointer cast in copy_n_and_update function will break the strict-aliasing rule [Closed]

> Username: westion717  
> Created at: 2018-01-30 03:19:09 UTC  
> Updated at: 2018-02-05 06:36:09 UTC  
> Closed at: 2018-02-04 22:37:36 UTC  
> Url: https://github.com/boostorg/container/issues/67  

https://github.com/boostorg/container/blob/9137957672d122c464df8779be506436f88e27f5/include/boost/container/detail/advanced_insert_int.hpp#L385  
  
A pointer cast above in copy_n_and_update function will break the strict-aliasing rule, and will get undefined behavior with aggressive optimization

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-01-30 13:23:42 UTC  
> Url: https://github.com/boostorg/container/issues/67#issuecomment-361591794  

I've updated Boost.Move's aligned storage to use an internal char array:  
  
https://github.com/boostorg/move/commit/a296628ce329a09c86a4e1bcdc306134f445568b  
  
I've modified Boost.Container to use this member as placement new target in commit:  
  
https://github.com/boostorg/container/commit/f0736ba73acb33b785f9e556c57b5f546aed6ddf  
  
Let me know if this fixes the issue.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2018-02-04 22:37:36 UTC  
> Url: https://github.com/boostorg/container/issues/67#issuecomment-362946309  

The commit fixes the warning but the aliasing warning with aligned storage might be a bug as reported by GCC issues:  
  
GCC 6: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=70054  
GCC 7: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=80593

---

## Comment 3

> Username: westion717  
> Created at: 2018-02-05 06:36:09 UTC  
> Url: https://github.com/boostorg/container/issues/67#issuecomment-362994187  

The fix can not fix aliasing breaking issue, please reopen it.
