# #654 fix: re-allow devicen_t with two color components [Merged]

> Username: striezel  
> Created at: 2022-04-29 20:41:51 UTC  
> Updated at: 2022-05-18 21:32:23 UTC  
> Merged at: 2022-05-18 07:30:07 UTC  
> Closed at: 2022-05-18 07:30:07 UTC  
> Url: https://github.com/boostorg/gil/pull/654  

### Description  
  
devicen_t<2> was possible in Boost 1.71, but broke in Boost 1.72.  
Now it's possible to use it again.  
  
Since there was no real need to disallow it in the first place, I adjusted the `static_assert` which prevented it to allow two components, too.  
  
### References  
  
Fixes #519.  
  
See that issue for more information.  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2022-05-18 07:28:49 UTC  
> Url: https://github.com/boostorg/gil/pull/654#issuecomment-1129668017  

Despite some jobs fail (w/ older clang and GCC 8 cxxstd=17) I'm happy to merge it. Thanks!

---

## Comment 2

> Username: striezel  
> Created_at: 2022-05-18 09:31:15 UTC  
> Url: https://github.com/boostorg/gil/pull/654#issuecomment-1129786368  

Thanks for merging it. :+1:  
  
The jobs for GCC 8 and older Clang variants also failed before that, see e. g. here: https://github.com/boostorg/gil/actions/runs/2321824140  
I don't know why the GCC 8 job fails, but in case of the Clang jobs it's just that there are no GitHub Actions runners with Ubuntu 16.04 anymore, and those older Clang jobs are still trying to use Ubuntu 16.04.  
  
That's why I mentioned that those Clang jobs should possibly be removed in https://github.com/boostorg/gil/pull/640#issue-1194914999:  
  
> Note: Some older Clang versions (Clang 3.8 and older) seem to be unavailable in the APT package repository, so those are not  
changed to 18.04. This is intended. Maybe tests for Clang 3.4 to Clang 3.8 should be removed, but that is probably worth another discussion.  
  
If removal of those jobs is not an option, then maybe someone should look into how to build with Clang 3.x on other, still available runners.

---

## Comment 3

> Username: mloskot  
> Created_at: 2022-05-18 21:22:38 UTC  
> Updated_at: 2022-05-18 21:32:22 UTC  
> Url: https://github.com/boostorg/gil/pull/654#issuecomment-1130564954  

> I don't know why the GCC 8 job fails  
  
We have seen similar failures where a particular compiler version with particular C+ version and build configuration variant generates segfaulting binary or it fails with internal compiler error. Either GIL stretches some compilers to the limits or there is a subtle bug that is hard to reproduce :-)  
  
So, I don't mind disabling such peculiar CI jobs. We are not testing compilers after all.  
  
UPDATE: Done in 4ad824e8dd379632809cf0b57478252b985fb5cf

---
