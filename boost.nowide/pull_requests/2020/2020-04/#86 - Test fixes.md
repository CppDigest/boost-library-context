# #86 Test fixes [Merged]

> Username: Flamefire  
> Created at: 2020-04-02 20:24:58 UTC  
> Updated at: 2020-04-03 17:19:23 UTC  
> Merged at: 2020-04-03 17:19:19 UTC  
> Closed at: 2020-04-03 17:19:19 UTC  
> Url: https://github.com/boostorg/nowide/pull/86  

test_system failed on MinGW/MSys due to differing environment blocks when started via bash.  
  
Enhance the tests with better error reporting and more focused tests to avoid that situation  
  
Also disable OSX-shared-lib tests in BJam and enable it on CI so the others are tested

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-04-02 22:25:47 UTC  
> Updated_at: 2020-04-03 16:29:09 UTC  
> Url: https://github.com/boostorg/nowide/pull/86#issuecomment-608123439  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/86?src=pr&el=h1) Report  
> Merging [#86](https://codecov.io/gh/boostorg/nowide/pull/86?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/nowide/commit/e2945b39838d2ceb28339da4392af09634a14d28&el=desc) will **increase** coverage by `1.67%`.  
> The diff coverage is `95.75%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #86      +/-   ##  
===========================================  
+ Coverage    89.65%   91.33%   +1.67%       
===========================================  
  Files           24       23       -1       
  Lines         2494     2481      -13       
  Branches       186      186                
===========================================  
+ Hits          2236     2266      +30       
+ Misses         252      209      -43       
  Partials         6        6                
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/86?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [test/test\_iostream.cpp](https://codecov.io/gh/boostorg/nowide/pull/86/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X2lvc3RyZWFtLmNwcA==) | `48.00% <51.42%> (+2.54%)` | :arrow_up: |  
| [test/test\_system.cpp](https://codecov.io/gh/boostorg/nowide/pull/86/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X3N5c3RlbS5jcHA=) | `98.11% <98.05%> (+5.66%)` | :arrow_up: |  
| [test/test\_codecvt.cpp](https://codecov.io/gh/boostorg/nowide/pull/86/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X2NvZGVjdnQuY3Bw) | `94.78% <100.00%> (+1.73%)` | :arrow_up: |  
| [test/test\_convert.cpp](https://codecov.io/gh/boostorg/nowide/pull/86/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X2NvbnZlcnQuY3Bw) | `100.00% <100.00%> (+5.19%)` | :arrow_up: |  
| [test/test\_env.cpp](https://codecov.io/gh/boostorg/nowide/pull/86/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X2Vudi5jcHA=) | `96.42% <100.00%> (+11.13%)` | :arrow_up: |  
| [test/test\_fs.cpp](https://codecov.io/gh/boostorg/nowide/pull/86/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X2ZzLmNwcA==) | `100.00% <100.00%> (+10.00%)` | :arrow_up: |  
| [test/test\_fstream.cpp](https://codecov.io/gh/boostorg/nowide/pull/86/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X2ZzdHJlYW0uY3Bw) | `98.59% <100.00%> (+1.07%)` | :arrow_up: |  
| [test/test\_fstream\_cxx11.cpp](https://codecov.io/gh/boostorg/nowide/pull/86/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X2ZzdHJlYW1fY3h4MTEuY3Bw) | `100.00% <100.00%> (+3.07%)` | :arrow_up: |  
| [test/test\_stackstring.cpp](https://codecov.io/gh/boostorg/nowide/pull/86/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X3N0YWNrc3RyaW5nLmNwcA==) | `100.00% <100.00%> (+2.18%)` | :arrow_up: |  
| [test/test\_stdio.cpp](https://codecov.io/gh/boostorg/nowide/pull/86/diff?src=pr&el=tree#diff-dGVzdC90ZXN0X3N0ZGlvLmNwcA==) | `98.76% <100.00%> (+4.57%)` | :arrow_up: |

---
