# #28 Added BOOST_HAS_PRAGMA_DETECT_MISSMATCH macro [Merged]

> Username: apolukhin  
> Created at: 2014-08-11 11:12:29 UTC  
> Updated at: 2014-08-19 16:53:53 UTC  
> Merged at: 2014-08-19 16:53:53 UTC  
> Closed at: 2014-08-19 16:53:53 UTC  
> Url: https://github.com/boostorg/config/pull/28  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-08-15 17:06:00 UTC  
> Url: https://github.com/boostorg/config/pull/28#issuecomment-52331680  

Is this really supported by all clang versions - surely it's windows specific?

---

## Comment 2

> Username: apolukhin  
> Created_at: 2014-08-18 09:12:49 UTC  
> Url: https://github.com/boostorg/config/pull/28#issuecomment-52468429  

> Is this really supported by all clang versions - surely it's windows specific?  
  
My fault. [Looks like](http://lists.cs.uiuc.edu/pipermail/cfe-commits/Week-of-Mon-20130603/080941.html) it was added to Clang 3.4 and requires `-fms-extension` flag during compilation.  
  
I've found no reliable way to check for `-fms-extension` flag. If you also have no idea how to check it I can totally remove BOOST_HAS_PRAGMA_DETECT_MISSMATCH macro for Clang.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2014-08-18 11:06:17 UTC  
> Url: https://github.com/boostorg/config/pull/28#issuecomment-52478467  

> > Is this really supported by all clang versions - surely it's windows specific?  
>   
> My fault. [Looks like](http://lists.cs.uiuc.edu/pipermail/cfe-commits/Week-of-Mon-20130603/080941.html) it was added to Clang 3.4 and requires `-fms-extension` flag during compilation.  
>   
> I've found no reliable way to check for `-fms-extension` flag. If you also have no idea how to check it I can totally remove BOOST_HAS_PRAGMA_DETECT_MISSMATCH macro for Clang.  
  
Does _MSC_VER do it?

---

## Comment 4

> Username: apolukhin  
> Created_at: 2014-08-18 11:36:45 UTC  
> Url: https://github.com/boostorg/config/pull/28#issuecomment-52480920  

> Does _MSC_VER do it?  
  
Possibly yes. [According to Clang manual](http://clang.llvm.org/docs/UsersManual.html#microsoft-extensions)  _MSC_VER will be defined to 1700 with that flag.  
  
So the `#define BOOST_HAS_PRAGMA_DETECT_MISSMATCH` in clang.hpp must look like the following:  
  
```  
// Detecting `-fms-extension` compiler flag assuming that _MSC_VER defined when that flag is used.  
#if defined (_MSC_VER) && (__clang_major__ > 3 || (__clang_major__ == 3 && __clang_minor__ >= 4))  
    // Ignoring the checks on APPLE platform because someone made __clang_major__ equal to  
    // LLVM version rather than compiler version.  
#   ifndef __APPLE__  
#       define BOOST_HAS_PRAGMA_DETECT_MISSMATCH  
#   endif  
#endif  
```  
  
If it's OK, I'll update the pull request.

---
