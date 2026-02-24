# #610 - Support negation of requirements [Open]

> Username: Flamefire  
> Created at: 2020-05-19 11:54:12 UTC  
> Updated at: 2021-05-29 17:22:39 UTC  
> Url: https://github.com/boostorg/build/issues/610  

If you want to build something on Windows only my current solution was: `<build>no <target-os>windows:<build>yes`   
  
However this causes conflicts when any other rule (e.g. C++11 requirements) adds a `<build>no` too  
  
Hence it would be great if negation like `<target-os>!windows:<build>no` would be supported  
  
The current solution involves a rather clunky `conditional` and a function

---

## Comment 1

> Username: mloskot  
> Created at: 2020-05-19 14:56:54 UTC  
> Url: https://github.com/boostorg/build/issues/610#issuecomment-630876096  

#594 is related as per https://lists.boost.org/Archives/boost/2020/05/248931.php

---

## Comment 2

> Username: Flamefire  
> Created at: 2020-05-19 15:07:49 UTC  
> Url: https://github.com/boostorg/build/issues/610#issuecomment-630883394  

If I read this correctly this already exists as `!<target-os>windows:<build>no`. Is this correct?

---

## Comment 3

> Username: Kojoley  
> Created at: 2020-05-19 21:49:12 UTC  
> Url: https://github.com/boostorg/build/issues/610#issuecomment-631102061  

Yes, but there seems to be another quirk that prevents to use negation on the first statement. I opened a PR with the error message reporting fix #611, and you are welcome to fix the bug itself.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-05-29 17:22:09 UTC  
> Url: https://github.com/boostorg/build/issues/610#issuecomment-850868282  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
