# #273 - --layout=versioned breaks FindBoost.cmake [Open]

> Username: rhalbersma  
> Created at: 2017-12-19 13:07:52 UTC  
> Updated at: 2021-06-26 03:10:22 UTC  
> Url: https://github.com/boostorg/build/issues/273  

As of the new 1.66 release, it appears that `--layout=versioned` generates the address-model into the library names. This was previously [discussed](https://groups.google.com/forum/#!searchin/boost-developers-archive/dimov$20x64%7Csort:date/boost-developers-archive/G4Bmy_FuBEM/lCKoAf-4BgAJ) on the developers mailinglist.  
  
On Linux, the dynamic link libraries have now the names in the following encoding (the x64 part is new):  
  
> libboost_unit_test_framework-gcc-mt-x64-1_66.so  
  
However, FindBoost.cmake (as of the latest CMake 3.10) is not aware of the x64 part in the library name.   
  
**Questions**: how can I suppress the x64 part using bjam while still using `--layout=versioned`? Or, alternatively, how can I tell CMake to look for a library with x64 in its name?

---

## Comment 1

> Username: pdimov  
> Created at: 2017-12-20 15:21:21 UTC  
> Url: https://github.com/boostorg/build/issues/273#issuecomment-353091693  

> how can I suppress the x64 part using bjam while still using --layout=versioned?  
  
You can't, sorry. I probably could have provided something like `--layout=versioned-noarch`, but it's too late now.  
  
> libboost_unit_test_framework-gcc-mt-x64-1_66.so  
  
I see that it also doesn't have the `gcc` version; that's probably a bug, but it's not my fault. :-)

---

## Comment 2

> Username: pdimov  
> Created at: 2017-12-20 17:28:46 UTC  
> Url: https://github.com/boostorg/build/issues/273#issuecomment-353128925  

FindBoost.cmake doesn't seem to yet know about 1.66:  
  
https://github.com/Kitware/CMake/blob/master/Modules/FindBoost.cmake#L793  
  
so presumably when it's updated it will also know about the architecture tag in the name.

---

## Comment 3

> Username: rhalbersma  
> Created at: 2017-12-20 22:28:58 UTC  
> Url: https://github.com/boostorg/build/issues/273#issuecomment-353201449  

I don't mind the new 32/64 bit labelling, as long as the folks @Kitware take notice to properly parse this when they add Boost 1.66 support in the next CMake release. Is there anyone on the release team in collaboration with them or should I submit an issue at their end?  
  
For now, I'm not above manually sym-linking Boost.Test. :)

---

## Comment 4

> Username: swatanabe  
> Created at: 2018-01-27 00:16:10 UTC  
> Url: https://github.com/boostorg/build/issues/273#issuecomment-360940527  

AMDG  
  
  I propose that we allow the following for the future:  
--layout=versioned-1.65  
  
  The meaning of this is to use the same format as  
at the given version.  We can drop old layouts  
after giving users a chance to catch up.  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:42 UTC  
> Url: https://github.com/boostorg/build/issues/273#issuecomment-868936511  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
