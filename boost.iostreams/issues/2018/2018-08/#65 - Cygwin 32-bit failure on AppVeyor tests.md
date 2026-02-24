# #65 - Cygwin 32-bit failure on AppVeyor tests [Closed]

> Username: eldiener  
> Created at: 2018-08-11 13:20:55 UTC  
> Updated at: 2018-08-20 15:26:52 UTC  
> Closed at: 2018-08-20 15:26:52 UTC  
> Url: https://github.com/boostorg/iostreams/issues/65  

On Appveyor tests for cygwin 32-bit I get failure with the message:  
  
[00:03:30] This problem is probably due to using incompatible versions of the cygwin DLL.  
[00:03:30] Search for cygwin1.dll using the Windows Start->Find/Search facility  
[00:03:30] and delete all but the most recent version.  The most recent version *should*  
[00:03:30] reside in x:\cygwin\bin, where 'x' is the drive on which you have  
[00:03:30] installed the cygwin distribution.  Rebooting is also suggested if you  
[00:03:30] are unable to find another cygwin DLL.  
  
This seems like some sort of setup problem for cygwin 32-bit under Appveyor.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-08-11 14:30:12 UTC  
> Url: https://github.com/boostorg/iostreams/issues/65#issuecomment-412278619  

This job just completed 10 hours ago on master and passed:  
  
https://ci.appveyor.com/project/eldiener/iostreams/branch/master  
  
Try rebuilding the develop build or triggering another.  Perhaps it was a transient issue on their side.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-08-20 12:16:30 UTC  
> Url: https://github.com/boostorg/iostreams/issues/65#issuecomment-414296454  

@eldiener since you currently own the iostreams appveyor project, you are the only one who can do this.

---

## Comment 3

> Username: eldiener  
> Created at: 2018-08-20 15:26:33 UTC  
> Url: https://github.com/boostorg/iostreams/issues/65#issuecomment-414358030  

Since it is now passing on the 'develop' branch it is fine.
