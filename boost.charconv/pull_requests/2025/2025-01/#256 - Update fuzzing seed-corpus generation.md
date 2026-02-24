# #256 Update fuzzing seed-corpus generation  [Open]

> Username: mborland  
> Created at: 2025-01-15 13:59:50 UTC  
> Updated at: 2025-01-15 21:04:30 UTC  
> Url: https://github.com/boostorg/charconv/pull/256  

Thanks to @Flamefire for providing this fix. Right now we have one file with many lines, but this breaks it into many files with one line each to better seed the fuzzer.

---

## Comment 1

> Username: Flamefire  
> Created_at: 2025-01-15 14:17:38 UTC  
> Url: https://github.com/boostorg/charconv/pull/256#issuecomment-2592974564  

Ping @grafikrobot in case he has any feedback on the Jam part. This looks quite involved for just running a Python script in Jam but I didn't found anything better.

---

## Review 2 [Commented]

> Username: grafikrobot  
> Created_at: 2025-01-15 14:34:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/256#pullrequestreview-2552924519  

📁 fuzzing/Jamfile

```diff
  23 |-     make /tmp/corpus/$(fuzzer) : : common.MkDir ;
  24 |-     make /tmp/mincorpus/$(fuzzer) : : common.MkDir ;
  48 |+     make $(corpus) : $(seed_files) : make-corpus ;
```

> Username: grafikrobot  
> Created_at: 2025-01-15 14:30:08 UTC  
> Updated_at: 2025-01-15 14:34:38 UTC  
> Url: https://github.com/boostorg/charconv/pull/256#discussion_r1916756499  

I like adding a dependency to the script make rules run. As it reruns the targets if it's only the script that changes.

> Username: mborland  
> Created_at: 2025-01-15 14:42:11 UTC  
> Url: https://github.com/boostorg/charconv/pull/256#discussion_r1916776176  

I'm not sure what you mean by this, or how to implement the suggestion

> Username: Flamefire  
> Created_at: 2025-01-15 14:43:33 UTC  
> Url: https://github.com/boostorg/charconv/pull/256#discussion_r1916779950  

Would that be this?  
```diff  
-    make $(corpus) : $(seed_files) : make-corpus ;  
+    make $(corpus) : $(seed_files) : make-corpus <dependency> $(.make-corpus-script) ;  
```  
  
BTW: Does it automatically add a dependency on the input files?

> Username: grafikrobot  
> Created_at: 2025-01-15 14:46:20 UTC  
> Url: https://github.com/boostorg/charconv/pull/256#discussion_r1916786238  

@Flamefire yes that would be it. And yes, the make rule adds dependencies for inputs (all B2 rules do).

> Username: grafikrobot  
> Created_at: 2025-01-15 14:48:12 UTC  
> Url: https://github.com/boostorg/charconv/pull/256#discussion_r1916789515  

Oops.. There should be an additional `:` there.. `make-corpus : <dependency> $(.make-corpus-script)`

> Username: mborland  
> Created_at: 2025-01-15 17:12:40 UTC  
> Url: https://github.com/boostorg/charconv/pull/256#discussion_r1917047843  

This is giving the error  
```  
error: '/home/runner/work/charconv/boost-root/libs/charconv/fuzzing/make-corpus.py' is not a valid property specification  
```  
  
https://github.com/boostorg/charconv/actions/runs/12792451062/job/35662937747?pr=256#step:7:44

> Username: grafikrobot  
> Created_at: 2025-01-15 19:28:32 UTC  
> Url: https://github.com/boostorg/charconv/pull/256#discussion_r1917230464  

There shouldn't be a space in the dependency. It should be `<dependency>$(.make-corpus-script)`.

---

📁 fuzzing/Jamfile

```diff
  41 |+     local fuzz_time = 60 ;
  42 |+     local corpus = /tmp/corpus/$(fuzzer) ;
  43 |+     local min_corpus = /tmp/mincorpus/$(fuzzer) ;
```

> Username: grafikrobot  
> Created_at: 2025-01-15 14:32:42 UTC  
> Updated_at: 2025-01-15 14:34:38 UTC  
> Url: https://github.com/boostorg/charconv/pull/256#discussion_r1916760886  

Does this need to run on Windows? If yes, these would need to refer to a valid Windows location also.

> Username: mborland  
> Created_at: 2025-01-15 14:43:30 UTC  
> Url: https://github.com/boostorg/charconv/pull/256#discussion_r1916779831  

Everything is tested linux only since it works out of the box

---

📁 fuzzing/Jamfile

```diff
  33 |+ actions make-corpus
  34 |+ {
  35 |+     "$(PYTHON:E=python)" "$(RUNNER)" $(<) $(>)
```

> Username: grafikrobot  
> Created_at: 2025-01-15 14:33:51 UTC  
> Updated_at: 2025-01-15 14:34:38 UTC  
> Url: https://github.com/boostorg/charconv/pull/256#discussion_r1916762742  

Wrap the target values with quotes in case of spaces in paths `"$(<)" "$(>)"`.


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2025-01-15 14:40:17 UTC  
> Updated_at: 2025-01-15 20:14:02 UTC  
> Url: https://github.com/boostorg/charconv/pull/256#issuecomment-2593040201  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/256?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.85%. Comparing base [(`dacb62a`)](https://app.codecov.io/gh/boostorg/charconv/commit/dacb62a218c97918914f976800730e7534b56e8e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`9ac6e57`)](https://app.codecov.io/gh/boostorg/charconv/commit/9ac6e5776fab1e33eab103b146385c3d058cae1e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/256/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/256?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #256   +/-   ##  
========================================  
  Coverage    94.85%   94.85%             
========================================  
  Files           69       69             
  Lines         9077     9077             
========================================  
  Hits          8610     8610             
  Misses         467      467             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/256?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/256?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [dacb62a...9ac6e57](https://app.codecov.io/gh/boostorg/charconv/pull/256?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: mborland  
> Created_at: 2025-01-15 21:04:29 UTC  
> Url: https://github.com/boostorg/charconv/pull/256#issuecomment-2593933167  

@Flamefire have you seen these directory not found errors in Locale? They seem sporadic

---
