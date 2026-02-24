# #39 Fix errno propagation in space(p, ec). [Merged]

> Username: cmuellner  
> Created at: 2016-12-11 23:48:33 UTC  
> Updated at: 2017-01-09 22:38:29 UTC  
> Merged at: 2017-01-09 22:37:44 UTC  
> Closed at: 2017-01-09 22:37:44 UTC  
> Url: https://github.com/boostorg/filesystem/pull/39  

The space(p, ec) implementation assigned a wrong  
error value to the given error_code object.  
Instead of getting the error value from errno,  
the code used the value '-1!=0'.  
  
Signed-off-by: Christoph Müllner <christophm30@gmail.com>

---

## Comment 1

> Username: cmuellner  
> Created_at: 2016-12-13 11:30:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/39#issuecomment-266715414  

A real-life problem, which is hit by the not propagated error value, can be found here:  
https://github.com/facebook/hhvm/pull/7534

---

## Comment 2

> Username: Beman  
> Created_at: 2017-01-09 22:38:29 UTC  
> Url: https://github.com/boostorg/filesystem/pull/39#issuecomment-271430566  

Thanks!  
  
--Beman

---
