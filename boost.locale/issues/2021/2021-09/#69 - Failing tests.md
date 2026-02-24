# #69 - Failing tests [Closed]

> Username: Flamefire  
> Created at: 2021-09-30 11:07:03 UTC  
> Updated at: 2022-10-02 20:48:33 UTC  
> Closed at: 2022-10-02 20:48:33 UTC  
> Url: https://github.com/boostorg/locale/issues/69  

With `./b2 toolset=gcc-10 libs/locale/test cxxstd=11 -j4 variant=debug` I see many failing tests with recent ICU  
  
One of those is "USD 1,345.00" instead of "USD1,345.00", other look more confusing.  
See https://github.com/Flamefire/locale/runs/3748013166?check_suite_focus=true or https://github.com/Flamefire/locale/actions in general  
  
@artyom-beilis As you wrote the tests I'm not sure if the tests are wrong or the results.

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2021-09-30 11:44:43 UTC  
> Url: https://github.com/boostorg/locale/issues/69#issuecomment-931246560  

I need to test myself see what happens.  
  
The test is problematic when ICU changes localization. Somethimes it is ICU bug. I'll check

---

## Comment 2

> Username: artyom-beilis  
> Created at: 2021-09-30 11:45:23 UTC  
> Url: https://github.com/boostorg/locale/issues/69#issuecomment-931246972  

What is ICU version? I can't find in the logs

---

## Comment 3

> Username: Flamefire  
> Created at: 2021-09-30 12:30:22 UTC  
> Url: https://github.com/boostorg/locale/issues/69#issuecomment-931277607  

On my machine:  
```  
/** The current ICU library version as a dotted-decimal string. The patchlevel  
 *  only appears in this string if it non-zero.  
 *  This value will change in the subsequent releases of ICU  
 *  @stable ICU 2.4  
 */  
#define U_ICU_VERSION "66.1"  
```

---

## Comment 4

> Username: Flamefire  
> Created at: 2022-06-30 19:54:17 UTC  
> Url: https://github.com/boostorg/locale/issues/69#issuecomment-1171617488  

Ok, in recent ICU versions (71.1 for me) the word-break after e.g. "Windows7" gets assigned `UBRK_WORD_NUMBER` only, not number&word as the test expects. The only thing related to this what I found is https://github.com/tc39/proposal-intl-segmenter/issues/80#issuecomment-494617128 where others also observed that any word ending in a number will be classified as a number.  
  
Not sure if that has always been the case. Do you have an ICU version where test_boundary succeeds?

---

## Comment 5

> Username: Flamefire  
> Created at: 2022-09-24 18:36:39 UTC  
> Updated at: 2022-09-27 19:53:43 UTC  
> Url: https://github.com/boostorg/locale/issues/69#issuecomment-1257039870  

I found a reason why `test_formatting` is failing sometimes after some [refactoring](https://github.com/boostorg/locale/pull/115):   
https://github.com/boostorg/locale/blob/6a47ccb02d8176f86b3bfe5448c107c817e5a741/src/boost/locale/icu/formatter.cpp#L258-L266  
  
I.e. if `cache` is given, then it used the "medium" length, otherwise the "full" length, see https://github.com/boostorg/locale/blob/76f6226fb12c900d0cc5c5744c30dc36a36d5b9f/src/boost/locale/icu/predefined_formatters.hpp#L40 Also the fallback `"yyyy-MM-dd HH:mm:ss"` doesn't look like "full" (not sure what that actually is though)  
  
And last but not least I found only 1 callsite of `strftime_to_icu_symbol` and that doesn't pass the `cache` hence it is always unset. So it looks like the cache was forgotten to be passed down after explicitly checking it at https://github.com/boostorg/locale/blob/76f6226fb12c900d0cc5c5744c30dc36a36d5b9f/src/boost/locale/icu/formatter.cpp#L503-L512  
  
@artyom-beilis Do you remember the intended semantics? I struggle to make sense out of that... Especially the check for (only!) [one of the cache entries above](https://github.com/boostorg/locale/blob/76f6226fb12c900d0cc5c5744c30dc36a36d5b9f/src/boost/locale/icu/formatter.cpp#L503-L512). What's its purpose?  
  
I'm referring to failures e.g. seen in https://github.com/boostorg/locale/actions/runs/3074715644/jobs/4967667878   
  
```  
Test: %c  
Error in line 452:   
---- [1970-02-05 15:33:13] != [Thursday, February 5, 1970 at 3:33:13 PM Greenwich Mean Time]  
Test: %x  
Error in line 452:   
---- [1970-02-05] != [Feb 5, 1970]  
Test: %X  
Error in line 452:   
---- [15:33:13] != [3:33:13 PM]  
Test: %c  
Error in line 452:   
---- [1970-02-05 15:33:13] != [Thursday, February 5, 1970 at 3:33:13 PM Greenwich Mean Time]  
Test: %x  
Error in line 452:   
---- [1970-02-05] != [Feb 5, 1970]  
Test: %X  
Error in line 452:   
---- [15:33:13] != [3:33:13 PM]  
```
