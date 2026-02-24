# #290 Split Spirit v2 tests [Merged]

> Username: Kojoley  
> Created at: 2017-11-25 14:17:09 UTC  
> Updated at: 2017-11-25 14:17:36 UTC  
> Merged at: 2017-11-25 14:17:33 UTC  
> Closed at: 2017-11-25 14:17:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/290  

I have splitted the Spirit v2 tests into subprojects to make it easier to run only a subset of tests.  
  
Also:  
 - The missing tests were added.  
 - Test names were fixed (missing prefix added and `-p3` suffix was removed).  
    This will make the regression matrix look like a chess table for a short while.  
 - `<cxxflags>-ftemplate-depth=512` replaced with `<c++-template-depth>512`

---
