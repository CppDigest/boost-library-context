# #16 Change to Sleep declaration for clang on Windows to match Windows imp… [Merged]

> Username: eldiener  
> Created at: 2015-07-22 11:02:52 UTC  
> Updated at: 2015-07-22 22:52:03 UTC  
> Merged at: 2015-07-22 22:52:03 UTC  
> Closed at: 2015-07-22 22:52:03 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/16  

…lementation being used.  
  
After discussions on the clang mailing list the change above has been made to sync the declaration of Sleep to the Windows implementation being used. With clang/mingw the original declaration is fine but otherwise for clang __declspec(dllimport) should be added to the declaration. The case where clang issues an error is arcane ( details of it can be provided if requested ) so syncing the declaration with the Windows implementation is safest. I have already made my appeal on the clang mailing list to stop issuing an error in this situation ( neither gcc or vc++ does so ).

---

## Comment 1

> Username: pdimov  
> Created_at: 2015-07-22 19:54:42 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/16#issuecomment-123844855  

You should only include `_mingw.h` when needed.

---

## Comment 2

> Username: eldiener  
> Created_at: 2015-07-22 22:28:38 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/16#issuecomment-123888900  

The _mingw.h header has already been included by boost/config.hpp whenever Windows is the platform and __MINGW32__ is defined. However I have updated the code as if I didn't know what boost/config.hpp was including, which is the right C++ thing to do.

---
