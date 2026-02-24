# #9 Changed operations to allow to be compiled on VxWorks [Closed]

> Username: RogerioDosSantos  
> Created at: 2014-12-03 20:57:27 UTC  
> Updated at: 2014-12-04 13:22:04 UTC  
> Closed at: 2014-12-04 12:26:09 UTC  
> Url: https://github.com/boostorg/filesystem/pull/9  

While on Linux system stat_vfs() and fstat_vfs() are used to gain information about a mounted volume, VxWorks uses dirLib routines statfs() and fstatfs(), that are POSIX-compliant routines to gain information about a file system.   
This change includes the proper header for VxWorks to to allow the proper access for those functions.

---

## Comment 1

> Username: Beman  
> Created_at: 2014-12-03 22:31:04 UTC  
> Updated_at: 2014-12-03 22:46:02 UTC  
> Url: https://github.com/boostorg/filesystem/pull/9#discussion_r21270994  

These lines do not make sense to me. Line 73 already includes <sys/stat.h>, so it looks to me as if the change here should be:  
  
+#          if !defined(**VXWORKS**)  
              include <sys/mount.h>  
+#          endif  
  
Am I missing something?  
  
--Beman

---

## Comment 2

> Username: Beman  
> Created_at: 2014-12-03 22:41:42 UTC  
> Url: https://github.com/boostorg/filesystem/pull/9#issuecomment-65505175  

Grrrr... GitHub massacred the code. Trying again:  
  
```  
+# if defined(__VXWORKS__)  
+# include <sys/stat.h>  
+# else  
#     include <sys/mount.h>  
+# endif  
```  
  
Line 73 already includes `# include <sys/stat.h>`, so it looks to me as if the change here should be:  
  
```  
 +# if +defined(__VXWORKS__)  
  #     include <sys/mount.h>  
 +# endif  
```  
  
Am I missing something?  
  
--Beman

---

## Comment 3

> Username: RogerioDosSantos  
> Created_at: 2014-12-03 22:43:00 UTC  
> Updated_at: 2014-12-03 22:46:02 UTC  
> Url: https://github.com/boostorg/filesystem/pull/9#discussion_r21271720  

My bad! You are right, as you pointed out stat.h does not need to be included and the correct is:  
  
+#     if !defined(**VXWORKS**)  
+#     include <sys/mount.h>  
+#     endif

---

## Comment 4

> Username: RogerioDosSantos  
> Created_at: 2014-12-03 22:54:07 UTC  
> Url: https://github.com/boostorg/filesystem/pull/9#issuecomment-65506970  

Hi Beman,  
  
Just to be sure that we are in sync: The stat.h should not be added again, however the mount.h should not be added. I changed the code :)  
  
Roger.

---

## Comment 5

> Username: Beman  
> Created_at: 2014-12-04 12:26:09 UTC  
> Url: https://github.com/boostorg/filesystem/pull/9#issuecomment-65624686  

Change applied to develop.  
  
I wanted to clean up some spacing, so applied the change manually.  
  
Thanks,  
  
--Beman

---

## Comment 6

> Username: RogerioDosSantos  
> Created_at: 2014-12-04 13:22:04 UTC  
> Url: https://github.com/boostorg/filesystem/pull/9#issuecomment-65630415  

Thank you  
  
On Thu Dec 04 2014 at 6:26:10 AM Beman Dawes notifications@github.com  
wrote:  
  
> Closed #9 https://github.com/boostorg/filesystem/pull/9.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/filesystem/pull/9#event-202587705.

---
