# #1092 Run and cover compile-test instantiation [Merged]

> Username: ckormanyos  
> Created at: 2024-02-13 12:06:14 UTC  
> Updated at: 2024-02-15 20:05:03 UTC  
> Merged at: 2024-02-14 20:19:23 UTC  
> Closed at: 2024-02-14 20:19:23 UTC  
> Url: https://github.com/boostorg/math/pull/1092  

The purpose of this PR is to cover the file [instantiate.hpp](https://github.com/boostorg/math/blob/develop/test/compile_test/instantiate.hpp).  
  
The file [instantiate.hpp](https://github.com/boostorg/math/blob/develop/test/compile_test/instantiate.hpp) has been extremely useful for compilation-syntactical correctness.  
  
It is/was not all that far away from actually running with `boost::float64_t`, which is exercised (with a few tens of modifications in the header) in this PR.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-02-14 10:46:03 UTC  
> Updated_at: 2024-02-14 11:31:50 UTC  
> Url: https://github.com/boostorg/math/pull/1092#issuecomment-1943505463  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1092?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: `6 lines` in your changes are missing coverage. Please review.  
> Comparison is base [(`f767420`)](https://app.codecov.io/gh/boostorg/math/commit/f767420c58839acac9a62829296c480e830245df?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 90.42% compared to head [(`86a0bbb`)](https://app.codecov.io/gh/boostorg/math/pull/1092?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 91.79%.  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1092/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1092?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1092      +/-   ##  
===========================================  
+ Coverage    90.42%   91.79%   +1.37%       
===========================================  
  Files          768      769       +1       
  Lines        63753    63826      +73       
===========================================  
+ Hits         57647    58592     +945       
+ Misses        6106     5234     -872       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1092?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/distributions/cauchy.hpp](https://app.codecov.io/gh/boostorg/math/pull/1092?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvY2F1Y2h5LmhwcA==) | `82.50% <ø> (ø)` | |  
| [.../boost/math/special\_functions/detail/bessel\_y0.hpp](https://app.codecov.io/gh/boostorg/math/pull/1092?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfeTAuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/math/special\_functions/lambert\_w.hpp](https://app.codecov.io/gh/boostorg/math/pull/1092?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2xhbWJlcnRfdy5ocHA=) | `83.57% <100.00%> (ø)` | |  
| [test/test\_compile\_test\_and\_run.cpp](https://app.codecov.io/gh/boostorg/math/pull/1092?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvbXBpbGVfdGVzdF9hbmRfcnVuLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/compile\_test/instantiate.hpp](https://app.codecov.io/gh/boostorg/math/pull/1092?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jb21waWxlX3Rlc3QvaW5zdGFudGlhdGUuaHBw) | `64.38% <95.04%> (+64.38%)` | :arrow_up: |  
  
... and [7 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1092/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1092?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1092?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f767420...86a0bbb](https://app.codecov.io/gh/boostorg/math/pull/1092?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2024-02-14 18:35:41 UTC  
> Updated_at: 2024-02-14 18:40:46 UTC  
> Url: https://github.com/boostorg/math/pull/1092#issuecomment-1944385991  

OK this one was very difficult for me.  
  
It clears up a lot of chaff.  
- Start running the many lines of [`instantiate.hpp`](https://github.com/boostorg/math/blob/develop/test/compile_test/instantiate.hpp). This required a bit of refactoring and range/memory checking.  
- Find and solve problems with `distributions`. If a distibution's `policy` (such as one of them instantiated with `default_policy`) does not have certain attributes, then these can't be checked for.  
  
I left about 500 more lines in [`instantiate.hpp`](https://github.com/boostorg/math/blob/develop/test/compile_test/instantiate.hpp) still needing coverage. These are in the so-called _mixed_ instantiations. But now that I figured out how this thing ticks and where the silly problems are/were, I'll handle those any time in the future with ease. Leave those 500 lines to me.  
  
Together with the minimalistic [`octonion measures`](https://github.com/boostorg/math/pull/1090) we are poised to get into the nineties of coverage, revealing the real and actual things-of-content that we need to address, instead of the chaff, which I've almost cleared out by now...  
  
This thingy needs to cycle green, then party.  
  
Cc: @jzmaddock and @mborland and @NAThompson

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2024-02-14 18:40:06 UTC  
> Url: https://github.com/boostorg/math/pull/1092#issuecomment-1944391977  

My personal best (or worst) 14 failed CI runs 'till I dialed it.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2024-02-14 18:48:00 UTC  
> Url: https://github.com/boostorg/math/pull/1092#issuecomment-1944402840  

By the way, this drone run is getting so bad that it's almost worse-than-neutral. It fails so often, that I disregard it almost by self-preservatoin, thereby nearly missing the few things-of-substance it finds. I'll think on it, if there is a better way to _reach_ some of those old/arcane compilers. Or we just have to seriously parse those outputs, which I do not like. I like green being green and red being red.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2024-02-14 20:19:27 UTC  
> Url: https://github.com/boostorg/math/pull/1092#issuecomment-1944529461  

Failures in drone are failures to obtain compiler

---

## Review 6 [Commented]

> Username: jzmaddock  
> Created_at: 2024-02-15 17:41:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1092#pullrequestreview-1883448927  

📁 include/boost/math/distributions/cauchy.hpp

```diff
 277 | template <class RealType, class Policy>
 278 | inline RealType mean(const cauchy_distribution<RealType, Policy>&)
 278 |- {  // There is no mean:
```

> Username: jzmaddock  
> Created_at: 2024-02-15 17:41:33 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491389877  

Apologies for being absent while this was being developed, just trying to catch up now....  
  
We really shouldn't remove the static_asserts here: the point is that the cauchy distribution has no mean, so calling `mean` on such a distribution is almost certainly a programming error that should be caught early (rather than at runtime).  There are a small number of situations where we're writing generic software which "works with any distribution" where we can change the Policy as a "get out of jail free card" to convert the compile time error to a runtime one.

> Username: ckormanyos  
> Created_at: 2024-02-15 17:46:39 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491395975  

I'm really glad to hear from you John. I was extremelty unsure about this. I came to the conclusion that (via default template parameters) the exact `policy` in question that was being used did not actually have any of the attributes needed for the `static_assert`.  
  
These `static_assert`s are being asserted on [`defualt_policy`](https://github.com/boostorg/math/blob/d4a42f930e92cc2ad4ca0d99d323f1159493c4ae/include/boost/math/policies/policy.hpp#L223), which is empty.  
  
So that thing either needs content, or the static assertions are bogus.  
  
I'm not entirely sure of this, but seem to have gleaned this from the code, ...

> Username: jzmaddock  
> Created_at: 2024-02-15 17:54:46 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491404887  

To be clear, the default is that these functions *do* static_assert, we should have added a test to that effect.... somewhere lost in the depths of time, I remember Paul and I having a long discussion about this, the "obvious" thing to do was to simply not provide functions for mean etc when those are undefined, but that then prevents the option of writing generic code.  So these need either a policy that contains `assert_undefined<false>`, or we need a `#define BOOST_MATH_ASSERT_UNDEFINED_POLICY false`.

> Username: jzmaddock  
> Created_at: 2024-02-15 17:57:08 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491407780  

Ahh, instantate.hpp includes the define at it's start, but that will not be triggered if policy.hpp has been included first... so I'm guilty of some fragile programming there :(

> Username: ckormanyos  
> Created_at: 2024-02-15 17:58:34 UTC  
> Updated_at: 2024-02-15 17:59:45 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491409337  

We could make the default policy have a bunch of constexpr stuff to pass the static assert tests. Or we could handle on the top layer with compiler switches.  
  
I expect we will find several things of this nature as we press on toward ninety five, more percent.  
  
This code base is awesome. Rarely, have I covered stuff with such depth and detail and established nature and found such deep thought going into it.  
  
But we will find a few thingy-ies needing tweaks and patches toward, 93, 95, 98, 99

> Username: ckormanyos  
> Created_at: 2024-02-15 18:10:41 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491422361  

> Ahh, `instantate.hpp` includes the define at it's start, but that will not be triggered if `policy.hpp` has been included first, ...  
  
Yeah! That is what I was missing. I studied that code for a while but did not dial that nuance.  
  
Can we repair this in this running PR? Or should i take another run at it. I really got a good start at this thing...

> Username: ckormanyos  
> Created_at: 2024-02-15 18:14:24 UTC  
> Updated_at: 2024-02-15 18:14:56 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491426568  

In other words @jzmaddock I'm looking to get about 1,500 lines covered if we can get these instantiations compiling and running in all situations. We are close. Maybe I need to include that policy and then restore those `static_asserts`. We are close to that in my latest PR.


---

## Review 7 [Commented]

> Username: jzmaddock  
> Created_at: 2024-02-15 17:55:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1092#pullrequestreview-1883473624  

📁 include/boost/math/special_functions/detail/bessel_y0.hpp

```diff
 146 |     static const char* function = "boost::math::bessel_y0<%1%>(%1%,%1%)";
 147 | 
 148 |+     static_cast<void>(function[0U]);
```

> Username: jzmaddock  
> Created_at: 2024-02-15 17:55:11 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491405561  

This can just be removed I think?

> Username: ckormanyos  
> Created_at: 2024-02-15 18:00:55 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491411941  

I'm ok with syntax stuff. I tend to over-cast and handle warnings like a maniac. And thus (as some would say) obfuscate. I'll get back to this later after we hash out these basics.

> Username: ckormanyos  
> Created_at: 2024-02-15 18:17:08 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491429463  

Last comment, I think some of those assrts are now known to be `domain_error`, not unknown.


---

## Review 8 [Commented]

> Username: jzmaddock  
> Created_at: 2024-02-15 18:37:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1092#pullrequestreview-1883551930  

📁 test/compile_test/instantiate.hpp

```diff
 336 |+       using std::fabs;
 337 |+ 
 338 |+       auto v_special { v1 };
```

> Username: jzmaddock  
> Created_at: 2024-02-15 18:37:55 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491452661  

Why not just set v_special to something specific that's in domain for acosh?  The fabs is unused as well.


---

## Review 9 [Commented]

> Username: jzmaddock  
> Created_at: 2024-02-15 18:38:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1092#pullrequestreview-1883553065  

📁 test/compile_test/instantiate.hpp

```diff
 388 |+       using std::exp;
 389 |+ 
 390 |+       while(v_special > exp(RealType(-1)))
```

> Username: jzmaddock  
> Created_at: 2024-02-15 18:38:38 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491453380  

As above, why not just pick a value?

> Username: ckormanyos  
> Created_at: 2024-02-15 18:39:57 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491454646  

Sure. I appreciate that


---

## Review 10 [Commented]

> Username: jzmaddock  
> Created_at: 2024-02-15 18:39:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1092#pullrequestreview-1883554477  

📁 test/compile_test/instantiate.hpp

```diff
 412 |-    boost::math::bernoulli_b2n<RealType>(i, i, &v1);
 470 |+    {
 471 |+       RealType* v1_array = new RealType[i];
```

> Username: jzmaddock  
> Created_at: 2024-02-15 18:39:33 UTC  
> Updated_at: 2024-02-15 18:39:34 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491454236  

Some scoped storage would be good ;)


---

## Review 11 [Commented]

> Username: jzmaddock  
> Created_at: 2024-02-15 18:39:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1092#pullrequestreview-1883554820  

📁 test/compile_test/instantiate.hpp

```diff
 414 |-    boost::math::tangent_t2n<RealType>(i, i, &v1);
 478 |+    {
 479 |+       RealType* v1_array = new RealType[i];
```

> Username: jzmaddock  
> Created_at: 2024-02-15 18:39:47 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491454456  

Ditto.

> Username: ckormanyos  
> Created_at: 2024-02-15 18:40:27 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491455818  

What do you mean? Like std::vector?

> Username: jzmaddock  
> Created_at: 2024-02-15 18:43:44 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491459587  

I was going to suggest a Boost solution, but these days it's spelled std::unique_ptr: https://en.cppreference.com/w/cpp/memory/unique_ptr


---

## Review 12 [Commented]

> Username: jzmaddock  
> Created_at: 2024-02-15 18:45:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1092#pullrequestreview-1883565744  

📁 test/compile_test/instantiate.hpp

```diff
 622 |+       using std::fabs;
 623 |+ 
 624 |+       auto v_special { v1 };
```

> Username: jzmaddock  
> Created_at: 2024-02-15 18:45:07 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491461510  

As above, specific value.

> Username: ckormanyos  
> Created_at: 2024-02-15 18:46:15 UTC  
> Updated_at: 2024-02-15 18:46:16 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491462891  

There are a bunch of those. I'm more concerned with how to get the exceptions and the instantiations running and compiling.


---

## Comment 13

> Username: jzmaddock  
> Created_at: 2024-02-15 18:52:16 UTC  
> Url: https://github.com/boostorg/math/pull/1092#issuecomment-1946953378  

Before I get too far into specific details - what is the purpose of this change?  
  
Let me explain: the file instantiate.hpp was designed for one purpose only - concept checking.  As a side effect, it also catches a few other things like missing `using std::whatever;` declarations, and (non-template) functions accidentally not marked as inline.  It was never really designed to be run, although that's not necessarily a bad thing in itself except if...  
  
Does this increase code coverage?  If so, that's arguably a bad thing... let me try and explain, instantiate.hpp does just that, it instantiates things, it doesn't anywhere actually check that they do the right thing!  In other words if there's missing coverage we shouldn't hide it behind something that's simply not designed for runtime testing, we should add some actual tests that validate behaviour.

---

## Review 14 [Commented]

> Username: jzmaddock  
> Created_at: 2024-02-15 18:57:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1092#pullrequestreview-1883589250  

📁 test/test_compile_test_and_run.cpp

```diff
  36 |+   const auto result_is_ok = (boost::report_errors() == 0);
  37 |+ 
  38 |+   return (result_is_ok ? 0 : -1);
```

> Username: jzmaddock  
> Created_at: 2024-02-15 18:57:09 UTC  
> Updated_at: 2024-02-15 18:57:10 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491475638  

Silly question: why not just return `boost::report_errors()` like everyone else? ;)

> Username: ckormanyos  
> Created_at: 2024-02-15 20:05:03 UTC  
> Updated_at: 2024-02-15 20:06:23 UTC  
> Url: https://github.com/boostorg/math/pull/1092#discussion_r1491588237  

> Silly question: why not just return `boost::report_errors()`  
  
We can work out these syntactic details. Thanks.  
  
> Does this increase code coverage?  
  
Yes it increases code coverage.  
  
> If so, that's arguably a bad thing.  
  
To me this doesn't make sense. I get that there can be compile checks. But when the arguments are fixed, the code should run. Maybe I missed some syntactic details when I got the code running. Then I made a few mistakes (maybe) and the code does not compile. But i think it should do both.  
  
We can hash out the syntactic details later, but I think the code in instantiate.hpp should both compile and run. Sure this might be a bit of a shocker in legacy code, but I would have a difficult time understanding why compiling exlcudes running (as long as the arguments are in range).


---

## Comment 15

> Username: ckormanyos  
> Created_at: 2024-02-15 18:58:34 UTC  
> Updated_at: 2024-02-15 19:00:04 UTC  
> Url: https://github.com/boostorg/math/pull/1092#issuecomment-1946974037  

> Before I get too far into specific details - what is the purpose of this change?  
  
That's a good question. When I started, I wanted the code to actually run, not just compile. I don't exactly remember when CI started failing. But when I got the code running in some situations, it started failing to compile in others.  
  
This was confusing to me. So I started a series of efforts to get the code compiling and running in all situations. This ultimately led to an actual change needed in the standalone configuration.  
  
Then I kind of lost track of it. honestly I do not know or remember the actual change which led to the `static_assert`s failing. But they seem to fail at times when I do not expect this. So that is unrelated to the running of the code.  
  
I think the code should compile and run. And my latest PR has this, except for one thing, the static asserts in `cauchy` and `non-central` do not work.

---

## Comment 16

> Username: ckormanyos  
> Created_at: 2024-02-15 19:01:48 UTC  
> Updated_at: 2024-02-15 19:02:57 UTC  
> Url: https://github.com/boostorg/math/pull/1092#issuecomment-1946984323  

I really do think the code in all situations should compile _and_ run. This might be a philosophical difference, but I don't see why they should exclude each other.  
  
And this is where I got stuck.  
  
If I kick out those 7 static asserts in Cauchy and non-central beta, I get both.  
  
So when I get the code compiling and running in all CI, I can not find the needed syntax to get compilation working for those distributions missing such things as skewness (when I include the `static_assert`s).

---

## Comment 17

> Username: ckormanyos  
> Created_at: 2024-02-15 19:04:28 UTC  
> Url: https://github.com/boostorg/math/pull/1092#issuecomment-1946992795  

There must be a way to get both. I have not yet found it. But I'm close in 3801e07c9b684f010e4650a6851a0eb667467644

---
