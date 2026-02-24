# #729 Fix mistakes [Merged]

> Username: ivanpanch  
> Created at: 2025-08-17 23:11:03 UTC  
> Updated at: 2025-08-18 11:15:35 UTC  
> Merged at: 2025-08-18 11:14:39 UTC  
> Closed at: 2025-08-18 11:14:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729  

I have corrected some minor mistakes in the documentation.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-08-18 06:12:02 UTC  
> Updated_at: 2025-08-18 10:38:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#issuecomment-3195260658  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/729?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 90.6%. Comparing base ([`3d32b38`](https://app.codecov.io/gh/boostorg/multiprecision/commit/3d32b38f57178736e1d90f56db121195fceae015?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`6e565d1`](https://app.codecov.io/gh/boostorg/multiprecision/commit/6e565d1879ae172bfd1bde6adb121d7bb3d2523d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/729/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/729?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff             @@  
##           develop    #729      +/-   ##  
==========================================  
- Coverage     94.7%   90.6%    -4.1%       
==========================================  
  Files          296     184     -112       
  Lines        30881   20115   -10766       
==========================================  
- Hits         29233   18210   -11023       
- Misses        1648    1905     +257       
```  
[see 152 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/729/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/729?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/729?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3d32b38...6e565d1](https://app.codecov.io/gh/boostorg/multiprecision/pull/729?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-08-18 06:13:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#issuecomment-3195263570  

Hi @ivanpanch I have just approved your workflow run. First-time contributors need approval for CI/CD runs. Your corrections seem really good. We are glad you found these and took the time to make numerous corrections.  
  
Since it's docs only, I suspect CI/CD will run green as expected. But let's make sure and check the CI run.

---

## Review 3 [Changes requested]

> Username: ckormanyos  
> Created_at: 2025-08-18 08:12:28 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/multiprecision/pull/729#pullrequestreview-3127264849  

There are a bunch of places where the word _or_ was changed to the word _and_. I believe most (if not all of these are incorrect). These are mostly in [reference_backend_requirements.qbk](https://github.com/boostorg/multiprecision/pull/729/commits/0532fa987e3700586915e4b822d9961fe247f74c)

---

## Comment 4

> Username: ivanpanch  
> Created_at: 2025-08-18 08:18:16 UTC  
> Updated_at: 2025-08-18 08:36:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#issuecomment-3195617753  

Consider “one of the two things”. Now let the two things be _A_ and _B_. This yields “one of _A_ and _B_”. In contrast, “_A_ or _B_” already means the same without the “one of” part. Bottom line: Out of multiple things (connected by _and_), one is taken.

---

## Review 5 [Changes requested]

> Username: ckormanyos  
> Created_at: 2025-08-18 08:24:41 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/multiprecision/pull/729#pullrequestreview-3127277662  

All in all it's getting better. I recommended a few minor reworks and I'd like to review once again with more time after you address these.

📁 doc/reference_backend_requirements.qbk

```diff
 242 | [[['Integer specific operations:]]]
 243 | [[`eval_modulus(b, a)`][`void`][Computes `b %= cb`, only required when `B` is an integer type.  The type of `a` shall be listed in one of the type lists
 244 |-             `B::signed_types`, `B::unsigned_types` or `B::float_types`.
```

> Username: ckormanyos  
> Created_at: 2025-08-18 08:15:34 UTC  
> Updated_at: 2025-08-18 08:24:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281634371  

For instance, I believe the word _or_ might be better than the owrd _and_ here

> Username: ckormanyos  
> Created_at: 2025-08-18 08:25:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281660522  

And there are numerous such (potentially wrong) changes i nthis file.

> Username: ivanpanch  
> Created_at: 2025-08-18 08:37:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281694407  

Consider “one of the two things”. Now let the two things be _A_ and _B_. This yields “one of _A_ and _B_”. In contrast, “_A_ or _B_” already means the same without the “one of” part. Bottom line: Out of multiple things (connected by _and_), one is taken.  
  
To make it less confusing, I can rewrite without “one of”, so that “or” is used.

> Username: ckormanyos  
> Created_at: 2025-08-18 10:12:37 UTC  
> Updated_at: 2025-08-18 10:12:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281955080  

Feel free to remove "one of". That will be clear enough.

> Username: ivanpanch  
> Created_at: 2025-08-18 10:40:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2282021350  

Done!


📁 doc/performance_integer_real_world.qbk

```diff
  19 | [table
  20 | [[Integer Type][Relative Performance (Actual time in parenthesis)]]
  21 |- [[checked_int1024_t][1.53714(0.0415328s)]]
```

> Username: ckormanyos  
> Created_at: 2025-08-18 08:16:58 UTC  
> Updated_at: 2025-08-18 08:24:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281637999  

I recommend to simply leave all the tables alone. They are auto-generated from actual code runs.

> Username: ivanpanch  
> Created_at: 2025-08-18 08:39:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281697405  

Okay, I can revert this. But I have also seen tables in your doc were a space is present.

> Username: ivanpanch  
> Created_at: 2025-08-18 10:17:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281966452  

Just to clarify, do you want me to revert this change?

> Username: ckormanyos  
> Created_at: 2025-08-18 10:30:27 UTC  
> Updated_at: 2025-08-18 10:31:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2282000136  

From this point, just do the minimum effort needed. So just leave them all with spaces now that they have spaces.  
  
I don't think we have time the change the performance code right now.. So some of the auto generated tables might (one future day) get generated without spaces. But it does not really matter so much.  
  
The real content of your PR is the spelling corrections, the redundant words that have been corrected, etc.  
  
Thx @ivanpanch

---

📁 doc/performance_integer_real_world.qbk

```diff
  39 | [table
  40 | [[Integer Type][Relative Performance (Actual time in parenthesis)]]
  41 |- [[checked_uint1024_t][9.52301(0.0422246s)]]
```

> Username: ckormanyos  
> Created_at: 2025-08-18 08:17:23 UTC  
> Updated_at: 2025-08-18 08:24:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281638968  

Same here. Auto-generated talbe. It's probably best to leave it alone.


📁 doc/reference_internal_support.qbk

```diff
  31 | 
  32 | The default version of this trait simply checks whether the ['kind] of conversion (for example from a floating-point to an integer type)
  33 |- is inherently lossy.  Note that if either of the types `From` or `To` are of an unknown number category (because `number_category` is not
```

> Username: ckormanyos  
> Created_at: 2025-08-18 08:19:03 UTC  
> Updated_at: 2025-08-18 08:24:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281643128  

either of the types `from` or `To` is...  
  
I believe we use either with or and is. Pretty sure of that but not entirely, in the sense of either this or that.

> Username: ivanpanch  
> Created_at: 2025-08-18 08:28:42 UTC  
> Updated_at: 2025-08-18 08:48:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281668774  

It would be correct to say “either the type … or the type …”. But here, you have “either **of** the types …”, i.e., either of **multiple** types.

> Username: ckormanyos  
> Created_at: 2025-08-18 10:14:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281960485  

I don't reall recall it might mean either one or the other or both. Hmmmm, let me check.

> Username: ckormanyos  
> Created_at: 2025-08-18 10:19:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281970025  

Including the spaces is probably fine. i don't think it actively breaks anything.


📁 doc/reference_number.qbk

```diff
 670 | 
 671 |- Also note that with the exception of `abs` that these functions can only be used with floating-point Backend types (if any other types
 671 |+ Also note that with the exception of `abs` these functions can only be used with floating-point Backend types (if any other types
```

> Username: ckormanyos  
> Created_at: 2025-08-18 08:19:29 UTC  
> Updated_at: 2025-08-18 08:24:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281643997  

I disagree with the correction

> Username: ivanpanch  
> Created_at: 2025-08-18 08:35:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281685442  

Please note that there is already a “that” in the sentence, the second “that” is not needed.

> Username: ckormanyos  
> Created_at: 2025-08-18 10:15:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281961062  

Agreed

---

📁 doc/reference_number.qbk

```diff
 695 | [[atan][1eps][0eps][0eps]]
 696 |- [[cosh][1045eps[footnote It's likely that the inherent error in the input values to our test cases are to blame here.]][0eps][0eps]]
 696 |+ [[cosh][1045eps[footnote It's likely that the inherent error in the input values to our test cases is to blame here.]][0eps][0eps]]
```

> Username: ckormanyos  
> Created_at: 2025-08-18 08:20:09 UTC  
> Updated_at: 2025-08-18 08:24:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281645580  

I'd go a bit farther here and write:  
"inherent errors in the input values to our test cases are..."

> Username: ivanpanch  
> Created_at: 2025-08-18 08:46:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281715959  

Done!


📁 doc/tutorial_numeric_limits.qbk

```diff
 120 | 
 121 |- There are number of proposals to
 121 |+ There is a number of proposals to
```

> Username: ckormanyos  
> Created_at: 2025-08-18 08:22:16 UTC  
> Updated_at: 2025-08-18 08:24:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281651023  

I would write:  
"There are various proposals to"

> Username: ivanpanch  
> Created_at: 2025-08-18 08:47:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281719455  

Done!


📁 example/numeric_limits_snips.cpp

```diff
 101 |   std::stringstream s;
 102 |-   s.precision(std::numeric_limits<double>::digits10); // or `float64_t` for 64-bit IEE754 double.
 102 |+   s.precision(std::numeric_limits<double>::digits10); // or `float64_t` for 64-bit IEEE 754 double.
```

> Username: ckormanyos  
> Created_at: 2025-08-18 08:23:54 UTC  
> Updated_at: 2025-08-18 08:24:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281656389  

IEEE754 is a thing. This is referring to it. No space is needed. And see if there are any other similar changes proposed please.

> Username: ivanpanch  
> Created_at: 2025-08-18 08:32:55 UTC  
> Updated_at: 2025-08-18 08:49:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281678720  

Note that an “E” was missing! I can remove the space, but it is written with a space here: https://standards.ieee.org/ieee/754/6210/

> Username: ckormanyos  
> Created_at: 2025-08-18 10:17:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#discussion_r2281966047  

> an “E” was missing!  
  
Yes. We definitely need three "E"s.  
  
I'm OK with the space. It's not a literary reference. But I suppose somewhere in the docs, we should have a literature reference to IEEE 754-2019. I can work that in at a future time.


---

## Comment 6

> Username: ckormanyos  
> Created_at: 2025-08-18 10:21:19 UTC  
> Updated_at: 2025-08-18 10:22:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#issuecomment-3196068487  

Ok @ivanpanch I think with just a few more changes (if you have the chance), this thing will be good to go. I fired up CI again, and it will not be influenced by the docs changes I hope.  
  
So if you could please do whatever you need to do to clarify the backend requirements, then Ithink we can move forward with this PR.  
  
Thanks for diving into this. I believe it definitely leaves the docs in a better state than before.  
  
I'm working on some unrelated docs clarifications in a separate branch. I'll push these in, but _much_ later.

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2025-08-18 11:15:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/729#issuecomment-3196225609  

Merged. To keep the change log a bit more organized, I opted for squash-merge.  
  
Thank you @ivanpanch the documents and code comments are much more corrrect and clear now.

---
