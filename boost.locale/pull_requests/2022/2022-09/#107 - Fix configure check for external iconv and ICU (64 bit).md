# #107 Fix configure check for external iconv and ICU (64 bit) [Merged]

> Username: Flamefire  
> Created at: 2022-09-11 09:36:31 UTC  
> Updated at: 2022-09-13 08:50:58 UTC  
> Merged at: 2022-09-13 08:49:09 UTC  
> Closed at: 2022-09-13 08:49:09 UTC  
> Url: https://github.com/boostorg/locale/pull/107  

More complete approach superseding #106 by @TheSalvator (thanks for proposing the fix which helped identifying the issue)  
  
Partial revert of 72a8e9eb75bd578b60d560f6c1f798af9cdfbf49  
  
The `exe` rule implicitly creates an `obj` rule for the given cpp file if it doesn't exist yet. This causes trouble when the cpp file is meant to be built with different options by multiple `exe` rules as the 2nd rule will reuse the `obj` rule from the first and hence ignores the different options such as the include path.  
  
Here it affects the `has_external_iconv` target due to the conflict with the `has_iconv` target. Hence add back an `obj` rule for the former. Fixes #63   
  
The same problem exists with the `has_icu*` targets. Fix it similarly.  
  
Closes #106  
  
@myakushka or @TheSalvator Can you test this patch?

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-09-11 12:23:43 UTC  
> Url: https://github.com/boostorg/locale/pull/107#issuecomment-1242953453  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/107?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#107](https://codecov.io/gh/boostorg/locale/pull/107?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d55ef3a) into [develop](https://codecov.io/gh/boostorg/locale/commit/c2b36419df84596ad605e92efe863f3c5788d5fc?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c2b3641) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #107   +/-   ##  
========================================  
  Coverage    80.97%   80.97%             
========================================  
  Files           76       76             
  Lines         5891     5891             
========================================  
  Hits          4770     4770             
  Misses        1121     1121             
```  
  
  
  
Help us with your feedback. Take ten seconds to tell us [how you rate us](https://about.codecov.io/nps?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Have a feature suggestion? [Share it here.](https://app.codecov.io/gh/feedback/?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)

---

## Comment 2

> Username: TheSalvator  
> Created_at: 2022-09-13 08:17:16 UTC  
> Url: https://github.com/boostorg/locale/pull/107#issuecomment-1245060559  

@Flamefire sorry for taking it so long. I've tested this changes (only with iconv for now) and it works :)  
  
If you need anything else to test, please let me know.  
Also let me know if you want to see the logs of the successful build and it's parameters.

---

## Comment 3

> Username: Flamefire  
> Created_at: 2022-09-13 08:49:02 UTC  
> Url: https://github.com/boostorg/locale/pull/107#issuecomment-1245096804  

@TheSalvator Happy to hear that this works. If it works with iconv then the issue is correctly identified and the ICU related change avoids a (potential) similar issue.  
So thanks for your help with this!

---

## Comment 4

> Username: TheSalvator  
> Created_at: 2022-09-13 08:50:58 UTC  
> Url: https://github.com/boostorg/locale/pull/107#issuecomment-1245098910  

@Flamefire Nice! Pleasure collaborating with you!

---
