# #86 Narrowing ref-qualifiers to compile on VS2012/2013 [Merged]

> Username: raffienficiaud  
> Created at: 2018-09-18 20:13:53 UTC  
> Updated at: 2018-09-19 14:10:09 UTC  
> Merged at: 2018-09-19 14:10:09 UTC  
> Closed at: 2018-09-19 14:10:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/86  

This should fix a compilation issue on VS2012/2013, although I have not tested it.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2018-09-19 11:11:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/86#issuecomment-422760879  

Tests are shown as failing:   
  
* on Travis, I think a worker died  
* on Appveyor, there is a compilation issue? I did not get it very well.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2018-09-19 14:10:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/86#issuecomment-422818690  

Good catch - that's the right fix, the CI failures are unrelated.

---
