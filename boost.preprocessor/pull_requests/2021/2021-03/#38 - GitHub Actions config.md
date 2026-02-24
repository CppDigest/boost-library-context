# #38 GitHub Actions config [Closed]

> Username: sdarwin  
> Created at: 2021-03-03 14:56:59 UTC  
> Updated at: 2024-12-25 17:14:33 UTC  
> Closed at: 2024-12-25 17:14:33 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/38  

GitHub Actions CI script, generated from the .travis.yml file. Please refer to https://github.com/CPPAlliance/githubactions for more information and instructions.

---

## Comment 1

> Username: eldiener  
> Created_at: 2021-03-03 17:14:50 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/38#issuecomment-789899727  

Why would I need this when I already have a travis.yml, an appveyor.yml, and your own drone.star ? Please explain what is accomplished by all this CI testing stuff. I readily admit that I find all this testing stuff confusing and a PITA in trying to understand how all this works or works together.

---

## Comment 2

> Username: sdarwin  
> Created_at: 2021-03-03 18:39:03 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/38#issuecomment-789965444  

@eldiener , answered in the Issue: https://github.com/CPPAlliance/githubactions/issues/1  
  
It is true that the tests are to a great degree redundant.   As long as travis, appveyor, and drone are all working, GitHub Actions may be overkill.     
  
But it can't hurt to have two systems running the same test, in case there's a failure, you can compare the results.

---

## Comment 3

> Username: eldiener  
> Created_at: 2021-03-04 01:14:39 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/38#issuecomment-790205305  

I really do not want to have CI running the same set of tests redundantly within different CI testing contexts. I am perfectly willing to defer to you or others as far as what is the most effective CI setup for this library, but I see no benefit of redundancy every time some change is made to the library.

---

## Comment 4

> Username: Flamefire  
> Created_at: 2024-12-25 16:42:23 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/38#issuecomment-2561948216  

@pdimov added a [CI config for GHA](https://github.com/boostorg/preprocessor/blob/develop/.github/workflows/ci.yml) so this can be closed

---
