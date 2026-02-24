# #31 isspace() may not be a macro [Closed]

> Username: kuhlenough  
> Created at: 2018-01-16 15:19:37 UTC  
> Updated at: 2018-05-08 00:30:18 UTC  
> Closed at: 2018-05-08 00:30:18 UTC  
> Url: https://github.com/boostorg/property_tree/pull/31  

Specifically in Dinkum clib shipped with VxWorks it's an inline function

---

## Comment 1

> Username: Kojoley  
> Created_at: 2018-02-20 00:27:40 UTC  
> Url: https://github.com/boostorg/property_tree/pull/31#issuecomment-366833788  

Did not you forget to strip `std::` from the call?

---

## Comment 2

> Username: kuhlenough  
> Created_at: 2018-02-20 14:53:13 UTC  
> Url: https://github.com/boostorg/property_tree/pull/31#issuecomment-367002129  

Yes, missed that last commit, fixed now.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2018-02-20 15:04:33 UTC  
> Url: https://github.com/boostorg/property_tree/pull/31#issuecomment-367005841  

Just to clarify: I am not a maintainer of property_tree (you should probably ping the boost mail list of inactivity of the maintainer), but for me it is not clear what was the original problem you described in the title. IIUC the real problem is that VxWorks does not have `isspace` in `std` namespace, so you want to use one in a global, right?

---

## Comment 4

> Username: kuhlenough  
> Created_at: 2018-02-20 15:24:32 UTC  
> Url: https://github.com/boostorg/property_tree/pull/31#issuecomment-367012608  

The original code was isaspace() in the global namespace, which matches a macro ctype.h in most C libraries.  But not the Dinkum clib used by VxWorks.    
  
I did not notice that the commit   
https://github.com/boostorg/property_tree/commit/616631208c11c47826f07037d8827052800ce98c  
already fixed this issue for VxWorks.  And now makes the code portable, but is not optimized for the most common user of boost glibc.    
  
So after your note I updated my pull, with the alternative, which is still portable,  and matches the macro and provide better performance for many users.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2018-02-20 15:29:50 UTC  
> Url: https://github.com/boostorg/property_tree/pull/31#issuecomment-367014354  

> I did not notice that the commit 6166312  
  
This commit was 2 years ago and in the boost since 1.61 :D. That's why your commit message is now wrong and confusing.

---
