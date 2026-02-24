# #658 - Alert end-users when changing the msvc-setup.bat directory structures [Open]

> Username: eldiener  
> Created at: 2020-09-22 15:16:35 UTC  
> Updated at: 2021-05-29 17:22:20 UTC  
> Url: https://github.com/boostorg/build/issues/658  

A recent fix to msvc.jam appears to have changed the msvc-setup.bat directory structure. You need to alert end-users about that since the old directory structure is not compatible with the new directory structure and the change meant that the new code assumed the new structure and would read a 32-bit msvc-setup.bat during a 64-bit build, causing obvious compile-link problems. Maybe when you change the directory structure you should set some variable in a file to tell you that this is the first time using the new directory structure and that you need to wipe out all files in the old structure for msvc-setup.bat so that all further builds work correctly with the new directory structure. I experienced this trying to run a 64-bit test with msvc-14.0 and realizing that the supposed msvc-setup.bat file for 64-bit builds was actually for 32-bit builds.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 17:21:50 UTC  
> Url: https://github.com/boostorg/build/issues/658#issuecomment-850868214  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
