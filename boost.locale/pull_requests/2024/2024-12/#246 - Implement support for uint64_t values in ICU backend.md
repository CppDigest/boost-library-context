# #246 Implement support for uint64_t values in ICU backend  [Merged]

> Username: Flamefire  
> Created at: 2024-12-05 19:54:36 UTC  
> Updated at: 2025-01-16 18:57:42 UTC  
> Merged at: 2025-01-16 18:10:36 UTC  
> Closed at: 2025-01-16 18:10:36 UTC  
> Url: https://github.com/boostorg/locale/pull/246  

ICU doesn't support uint64_t directly but provides access to formatting and parsing of decimal number strings.  
  
Use Boost.Charconv to interface with that such that values larger than `INT64_MAX` can be formatted correctly and parsed at all.  
  
Fixes https://github.com/boostorg/locale/issues/235

---

## Comment 1

> Username: mborland  
> Created_at: 2025-01-13 14:40:59 UTC  
> Url: https://github.com/boostorg/locale/pull/246#issuecomment-2587286333  

`try_scientific_to_int` looks good to me. The one recommendation I would have is to add fuzzing to it. The charconv fuzzer is in this folder: https://github.com/boostorg/charconv/tree/develop/fuzzing, and the associated CI run is https://github.com/boostorg/charconv/blob/develop/.github/workflows/fuzz.yml. In the fuzzing folder that Jamfile is already setup to run every `.cpp` file in the folder for 30 seconds, and looks for a text file in the seed corpus folder of the same name. In the seed corpus text file add examples of what properly formatted input looks like to seed the fuzzer. ChatGPT can probably spit out a good python program to generate that text file for you. I found lots of small errors throughout charconv with fuzzing I never would have found myself.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2025-01-14 19:11:14 UTC  
> Url: https://github.com/boostorg/locale/pull/246#issuecomment-2590906657  

> `try_scientific_to_int` looks good to me. The one recommendation I would have is to add fuzzing to it.  
> [...]  
>  I found lots of small errors throughout charconv with fuzzing I never would have found myself.  
  
Not sure what that should show. The input is pretty restricted as it is the intermediate result of ICU, so should already be well formed. A problem I anticipate is a logic bug in the code yielding wrong results but the fuzzer wouldn't find that, would it?     
AFAIK fuzzing just triggers asserts and memory errors. Or what kind of issues have you found?  
  
Anyway working on that in a [new branch](https://github.com/boostorg/locale/tree/feature/fuzzing) to try it. Can't hurt.

---

## Comment 3

> Username: mborland  
> Created_at: 2025-01-14 19:35:27 UTC  
> Url: https://github.com/boostorg/locale/pull/246#issuecomment-2590951256  

> > `try_scientific_to_int` looks good to me. The one recommendation I would have is to add fuzzing to it.  
> > [...]  
> > I found lots of small errors throughout charconv with fuzzing I never would have found myself.  
>   
> Not sure what that should show. The input is pretty restricted as it is the intermediate result of ICU, so should already be well formed. A problem I anticipate is a logic bug in the code yielding wrong results but the fuzzer wouldn't find that, would it? AFAIK fuzzing just triggers asserts and memory errors. Or what kind of issues have you found?  
>   
> Anyway working on that in a [new branch](https://github.com/boostorg/locale/tree/feature/fuzzing) to try it. Can't hurt.  
  
Fuzzing helped me fix logic errors. In the `old_crashes` folder you'll see things that previously broke the logic or hit asserts. One that might impact you is something like this: https://github.com/boostorg/charconv/blob/develop/fuzzing/old_crashes/fuzz_from_chars_float/crash-9a5e44f5d633b9e38560345546d787a8f6b23ba6. You could try related items like `5E`, `5e`, `5.5e` etc. since those should be malformed for you.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2025-01-16 17:29:33 UTC  
> Updated_at: 2025-01-16 18:57:42 UTC  
> Url: https://github.com/boostorg/locale/pull/246#issuecomment-2596310728  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/246?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.97%. Comparing base [(`5f24abe`)](https://app.codecov.io/gh/boostorg/locale/commit/5f24abe113ad779360eeebbefa146bbe3a37d271?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`d56bad6`)](https://app.codecov.io/gh/boostorg/locale/commit/d56bad6d6963b5b174da8c2fffef7e3e3fa62ff8?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 11 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/246/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/246?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #246      +/-   ##  
===========================================  
+ Coverage    95.87%   95.97%   +0.09%       
===========================================  
  Files          119      119                
  Lines        10410    10611     +201       
===========================================  
+ Hits          9981    10184     +203       
+ Misses         429      427       -2       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/locale/pull/246?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/icu/formatter.cpp](https://app.codecov.io/gh/boostorg/locale/pull/246?src=pr&el=tree&filepath=src%2Ficu%2Fformatter.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2ljdS9mb3JtYXR0ZXIuY3Bw) | `82.45% <100.00%> (+1.93%)` | :arrow_up: |  
| [src/icu/formatter.hpp](https://app.codecov.io/gh/boostorg/locale/pull/246?src=pr&el=tree&filepath=src%2Ficu%2Fformatter.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2ljdS9mb3JtYXR0ZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [src/icu/numeric.cpp](https://app.codecov.io/gh/boostorg/locale/pull/246?src=pr&el=tree&filepath=src%2Ficu%2Fnumeric.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2ljdS9udW1lcmljLmNwcA==) | `95.68% <100.00%> (-0.15%)` | :arrow_down: |  
| [src/util/numeric\_conversion.hpp](https://app.codecov.io/gh/boostorg/locale/pull/246?src=pr&el=tree&filepath=src%2Futil%2Fnumeric_conversion.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3V0aWwvbnVtZXJpY19jb252ZXJzaW9uLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/formatting\_common.hpp](https://app.codecov.io/gh/boostorg/locale/pull/246?src=pr&el=tree&filepath=test%2Fformatting_common.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9mb3JtYXR0aW5nX2NvbW1vbi5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [test/show\_config.cpp](https://app.codecov.io/gh/boostorg/locale/pull/246?src=pr&el=tree&filepath=test%2Fshow_config.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9zaG93X2NvbmZpZy5jcHA=) | `100.00% <ø> (ø)` | |  
| [test/test\_formatting.cpp](https://app.codecov.io/gh/boostorg/locale/pull/246?src=pr&el=tree&filepath=test%2Ftest_formatting.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Zvcm1hdHRpbmcuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_posix\_formatting.cpp](https://app.codecov.io/gh/boostorg/locale/pull/246?src=pr&el=tree&filepath=test%2Ftest_posix_formatting.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3Bvc2l4X2Zvcm1hdHRpbmcuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_std\_formatting.cpp](https://app.codecov.io/gh/boostorg/locale/pull/246?src=pr&el=tree&filepath=test%2Ftest_std_formatting.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N0ZF9mb3JtYXR0aW5nLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_util\_numeric\_convert.cpp](https://app.codecov.io/gh/boostorg/locale/pull/246?src=pr&el=tree&filepath=test%2Ftest_util_numeric_convert.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3V0aWxfbnVtZXJpY19jb252ZXJ0LmNwcA==) | `100.00% <100.00%> (ø)` | |  
| ... and [1 more](https://app.codecov.io/gh/boostorg/locale/pull/246?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/246?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/246?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5f24abe...d56bad6](https://app.codecov.io/gh/boostorg/locale/pull/246?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
