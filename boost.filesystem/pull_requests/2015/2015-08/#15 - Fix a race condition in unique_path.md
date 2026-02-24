# #15 Fix a race condition in unique_path. [Merged]

> Username: CornedBee  
> Created at: 2015-08-27 09:46:19 UTC  
> Updated at: 2015-08-28 14:13:12 UTC  
> Merged at: 2015-08-28 13:30:38 UTC  
> Closed at: 2015-08-28 13:30:38 UTC  
> Url: https://github.com/boostorg/filesystem/pull/15  

If two threads call unique_path at the same time for the first time in the program run,  
both initial calls to CryptAcquireContext can fail. Both threads will then call the function  
with CRYPT_NEWKEYSET, but only one of these threads can succeed. The other will  
fail with NTE_EXISTS.  
  
This patch makes it so that if a call fails with that error, it will try to call without the  
flag one more time, in case another thread created the key set in the meantime.  
  
This also applies the patch from trac report #7506. Using these additional flags  
is the right thing to do.

---

## Comment 1

> Username: Beman  
> Created_at: 2015-08-28 14:13:12 UTC  
> Url: https://github.com/boostorg/filesystem/pull/15#issuecomment-135784284  

Thanks, Sebastian,  
  
--Beman

---
