# #658 drone generator supports conjunctions [Merged]

> Username: alandefreitas  
> Created at: 2023-01-12 23:11:13 UTC  
> Updated at: 2023-07-24 14:36:40 UTC  
> Merged at: 2023-01-20 02:50:11 UTC  
> Closed at: 2023-01-20 02:50:11 UTC  
> Url: https://github.com/boostorg/url/pull/658  



---

## Comment 1

> Username: alandefreitas  
> Created_at: 2023-01-12 23:16:36 UTC  
> Updated_at: 2023-01-12 23:18:06 UTC  
> Url: https://github.com/boostorg/url/pull/658#issuecomment-1381096480  

This should allow conjunctions inside the disjunctions, such as `gcc >5 <7 || >=12 <=14`.   
  
I think most projects still want to keep the upper bound open so that new compilers are included automatically but it's still worth having the ability to remove a compiler from CI from time to time.  
  
I also included comments on the functions to make it easier to update. In most cases, we just need to update the list of supported compilers.

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-01-12 23:25:32 UTC  
> Url: https://github.com/boostorg/url/pull/658#issuecomment-1381101690  

GCOVR code coverage report [https://658.url.prtest.cppalliance.org/gcovr/index.html](https://658.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://658.url.prtest.cppalliance.org/genhtml/index.html](https://658.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://658.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://658.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 3

> Username: sdarwin  
> Created_at: 2023-01-12 23:29:57 UTC  
> Url: https://github.com/boostorg/url/pull/658#issuecomment-1381104082  

The added comments are very nice. and being able to specify those ranges.  
  
`warnings_as_errors=True` was removed.     boostorg/json used that,  has it been moved to another file?

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-01-12 23:35:01 UTC  
> Url: https://github.com/boostorg/url/pull/658#issuecomment-1381106854  

GCOVR code coverage report [https://658.url.prtest.cppalliance.org/gcovr/index.html](https://658.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://658.url.prtest.cppalliance.org/genhtml/index.html](https://658.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://658.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://658.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 5

> Username: alandefreitas  
> Created_at: 2023-01-12 23:41:28 UTC  
> Url: https://github.com/boostorg/url/pull/658#issuecomment-1381110333  

> warnings_as_errors=True was removed. boostorg/json used that, has it been moved to another file?  
  
Oh... I forgot to mention that.   
  
According to Peter we should never pass `B2_CXXFLAGS` on the command line. It should all be in the `Jamfile`, where we can enable it only for the targets and compilers that make sense.  
  
Boost.URL uses no `B2_CXXFLAGS` anymore. It caused a lot of problems because it can't be later disabled with `<warnings-as-errors>off` on the `Jamfile`. `B2_CXXFLAGS` always overrides the `b2` options, making them ineffective. In other words, the generator option was only there to incentivize bad behavior.  
  
Now that I'm thinking about it a little more if the generator option were to stay and we were to pass it in the command line, we should at least enable b2's `warnings-as-errors=on` on the cmd line instead of using `B2_CXXFLAGS`. It's not the best alternative but it's better than before.

---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2023-01-13 00:02:57 UTC  
> Updated_at: 2023-01-19 18:56:50 UTC  
> Url: https://github.com/boostorg/url/pull/658#issuecomment-1381124943  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/658?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#658](https://codecov.io/gh/boostorg/url/pull/658?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (664343e) into [develop](https://codecov.io/gh/boostorg/url/commit/4e1f5d0959df717d9abf1d4448a31199bd29f665?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (4e1f5d0) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/658/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/658?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #658   +/-   ##  
========================================  
  Coverage    96.89%   96.89%             
========================================  
  Files          146      146             
  Lines         7916     7916             
========================================  
  Hits          7670     7670             
  Misses         246      246             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/658?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/658?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [4e1f5d0...664343e](https://codecov.io/gh/boostorg/url/pull/658?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 7

> Username: sdarwin  
> Created_at: 2023-01-13 17:09:21 UTC  
> Url: https://github.com/boostorg/url/pull/658#issuecomment-1382148345  

might not be doing this right so let me know.  
  
```  
  generatedjobs = generate(  
        # Compilers  
        ['gcc >=4.8',  
         'clang >=3.8 <=13',  
        ...  
```  
  
In the above example, it ignores `<=13` and builds all clangs.  
  
```  
  generatedjobs = generate(  
        # Compilers  
        ['gcc >=4.8',  
         'clang >=3.8 <13',  
        ...  
```  
In the above example, it didn't build any clangs.

---

## Comment 8

> Username: alandefreitas  
> Created_at: 2023-01-13 17:45:02 UTC  
> Url: https://github.com/boostorg/url/pull/658#issuecomment-1382185270  

> In the above example, it ignores <=13 and builds all clangs.  
  
I found the bug.   
  
When applying the comparator  
  
```  
                if v[0] < comparator[1]:  
                    match = True  
                elif v[0] > comparator[1]:  
                    match = False  
#...  
```  
  
the `<` operator had some `if`s instead of `elif`.   
  
I tested a few combinations and it should be fixed now.

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-01-13 17:52:04 UTC  
> Url: https://github.com/boostorg/url/pull/658#issuecomment-1382191673  

GCOVR code coverage report [https://658.url.prtest.cppalliance.org/gcovr/index.html](https://658.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://658.url.prtest.cppalliance.org/genhtml/index.html](https://658.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://658.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://658.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-01-13 18:03:56 UTC  
> Url: https://github.com/boostorg/url/pull/658#issuecomment-1382203100  

GCOVR code coverage report [https://658.url.prtest.cppalliance.org/gcovr/index.html](https://658.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://658.url.prtest.cppalliance.org/genhtml/index.html](https://658.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://658.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://658.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 11

> Username: sdarwin  
> Created_at: 2023-01-13 19:42:59 UTC  
> Url: https://github.com/boostorg/url/pull/658#issuecomment-1382313849  

>  it should be fixed now.  
  
Yes. At least those combinations are working.  Should be ok to merge.

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2023-01-19 16:40:30 UTC  
> Url: https://github.com/boostorg/url/pull/658#issuecomment-1397279807  

GCOVR code coverage report [https://658.url.prtest.cppalliance.org/gcovr/index.html](https://658.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://658.url.prtest.cppalliance.org/genhtml/index.html](https://658.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://658.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://658.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2023-01-19 18:50:47 UTC  
> Url: https://github.com/boostorg/url/pull/658#issuecomment-1397450489  

GCOVR code coverage report [https://658.url.prtest.cppalliance.org/gcovr/index.html](https://658.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://658.url.prtest.cppalliance.org/genhtml/index.html](https://658.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://658.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://658.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---
