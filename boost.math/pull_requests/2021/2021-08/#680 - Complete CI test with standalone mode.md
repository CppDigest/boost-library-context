# #680 Complete CI test with standalone mode [Merged]

> Username: mborland  
> Created at: 2021-08-24 18:07:17 UTC  
> Updated at: 2022-01-14 17:12:00 UTC  
> Merged at: 2022-01-14 16:12:23 UTC  
> Closed at: 2022-01-14 16:12:23 UTC  
> Url: https://github.com/boostorg/math/pull/680  

Re-allocates the G++10 CI run to test the complete CI battery using standalone mode.

---

## Comment 1

> Username: mborland  
> Created_at: 2021-08-25 19:14:40 UTC  
> Updated_at: 2021-08-25 20:25:27 UTC  
> Url: https://github.com/boostorg/math/pull/680#issuecomment-905804405  

@NAThompson So good news this works. Bad news is it looks like there's a fair bit of work required to make it green.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2021-08-25 20:42:30 UTC  
> Url: https://github.com/boostorg/math/pull/680#issuecomment-905859334  

yikes. CI is a mixed blessing for sure.

---

## Comment 3

> Username: mborland  
> Created_at: 2022-01-04 10:31:23 UTC  
> Url: https://github.com/boostorg/math/pull/680#issuecomment-1004694168  

Now that Boost.Multiprecision has a beta of it's own standalone mode I am having more success in getting the tests to pass locally without butchering everything.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2022-01-04 15:03:48 UTC  
> Url: https://github.com/boostorg/math/pull/680#issuecomment-1004886547  

wowsers what a diff.  
  
Everything I managed to read looks good.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2022-01-04 17:42:48 UTC  
> Url: https://github.com/boostorg/math/pull/680#issuecomment-1005034089  

> wowsers what a diff.  
  
Amazing (ly cool) indeed.  
  
That's also kind of what I found. it was not possible to embedd larger parts of math on the metal in a standalone fashion until multiprecision got close to standalone. This is a true unison endeavor i think.

---

## Review 6 [Commented]

> Username: NAThompson  
> Created_at: 2022-01-05 14:58:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/680#pullrequestreview-844699101  

📁 include/boost/math/tools/config.hpp

```diff
  36 |+ 
  37 |+ // Clang 13 on mac provides the execution header with none of the functionality. TODO: Check back on this
  38 |+ #if !__has_include(<execution>) || (defined(__APPLE__) && defined(__clang__))
```

> Username: NAThompson  
> Created_at: 2022-01-05 14:58:38 UTC  
> Url: https://github.com/boostorg/math/pull/680#discussion_r778886703  

Can you get the Clang version in a macro?

> Username: mborland  
> Created_at: 2022-01-05 15:05:58 UTC  
> Url: https://github.com/boostorg/math/pull/680#discussion_r778893144  

I edited the comment to make it more clear since it looks like no version of apple clang supports it ([see Standardization of Parallelism TS](https://en.cppreference.com/w/cpp/compiler_support)).


---

## Review 7 [Commented]

> Username: NAThompson  
> Created_at: 2022-01-05 15:00:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/680#pullrequestreview-844700935  

📁 test/centered_continued_fraction_test.cpp

```diff
   9 | #include <boost/math/tools/centered_continued_fraction.hpp>
  10 | #include <boost/math/constants/constants.hpp>
  11 |+ #include <boost/core/demangle.hpp>
```

> Username: NAThompson  
> Created_at: 2022-01-05 15:00:02 UTC  
> Updated_at: 2022-01-05 15:00:03 UTC  
> Url: https://github.com/boostorg/math/pull/680#discussion_r778888290  

I have added a demangler to `math_unit_test.hpp` to facilitate standalone; it's available via `detail::demangle`.


---

## Review 8 [Commented]

> Username: NAThompson  
> Created_at: 2022-01-06 19:03:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/680#pullrequestreview-845961317  

📁 include/boost/math/tools/polynomial.hpp

```diff
 303 |    polynomial(I first, unsigned length)
 304 |-       : m_data(first, std::next(first, length))
 304 |+       : m_data(first, std::next(first, length + 1))
```

> Username: NAThompson  
> Created_at: 2022-01-06 19:03:20 UTC  
> Url: https://github.com/boostorg/math/pull/680#discussion_r779787254  

Wait . . what happened here?

> Username: mborland  
> Created_at: 2022-01-06 19:07:26 UTC  
> Url: https://github.com/boostorg/math/pull/680#discussion_r779789626  

I sized this constructor wrong and it broke the tests; the pointer constructor uses `length + 1`. MSVC needed a constructor for iterators.


---

## Review 9 [Commented]

> Username: NAThompson  
> Created_at: 2022-01-06 19:03:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/680#pullrequestreview-845961842  

📁 test/math_unit_test.hpp

```diff
  35 | #else
  36 |-         return name;
  36 |+         std::string s {name};
```

> Username: NAThompson  
> Created_at: 2022-01-06 19:03:56 UTC  
> Url: https://github.com/boostorg/math/pull/680#discussion_r779787620  

Apparently there is a demangler on Windows that would make this a bit cleaner. Do you have a Windows box?

> Username: mborland  
> Created_at: 2022-01-06 19:09:54 UTC  
> Url: https://github.com/boostorg/math/pull/680#discussion_r779792340  

I don't right now because I am about to move back across the pond. I am going to revert back to `boost::core::demangle` because I am still getting a memory leak on the GCC CI test that I can't replicate with clang.

> Username: NAThompson  
> Created_at: 2022-01-06 19:25:03 UTC  
> Url: https://github.com/boostorg/math/pull/680#discussion_r779806160  

Oh yeah . . . I thought std::string taking ownership of malloc'd char* was standardized now, but I guess not . . .


---

## Comment 10

> Username: mborland  
> Created_at: 2022-01-09 07:49:55 UTC  
> Url: https://github.com/boostorg/math/pull/680#issuecomment-1008247219  

@jzmaddock Does [this linker error](https://github.com/boostorg/math/runs/4740775982?check_suite_focus=true#step:18:271) from multi-precision make sense to you?

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2022-01-09 09:24:06 UTC  
> Url: https://github.com/boostorg/math/pull/680#issuecomment-1008261305  

> Does this linker error from multi-precision make sense to you?  
  
I think it's a missing #include of `<boost/math/special_functions/factorial.hpp>`, was probably getting implicitly included before?

---

## Comment 12

> Username: NAThompson  
> Created_at: 2022-01-10 17:47:22 UTC  
> Url: https://github.com/boostorg/math/pull/680#issuecomment-1009173403  

This looks good to me. Since it's a huge diff I think @jzmaddock should also sign off.

---

## Review 13 [Commented]

> Username: jzmaddock  
> Created_at: 2022-01-11 18:05:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/680#pullrequestreview-849558136  

📁 include/boost/math/special_functions/detail/unchecked_factorial.hpp

```diff
 981 | 
 982 |+ #endif // BOOST_NO_LEXICAL_CAST
 983 |+ 
```

> Username: jzmaddock  
> Created_at: 2022-01-11 18:05:43 UTC  
> Updated_at: 2022-01-11 18:05:44 UTC  
> Url: https://github.com/boostorg/math/pull/680#discussion_r782406041  

I don't think this is quite right yet, but we'll look at this again after merging as I think the multiprecision case could in general be improved here.


---

## Review 14 [Commented]

> Username: jzmaddock  
> Created_at: 2022-01-11 18:10:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/680#pullrequestreview-849562998  

📁 include/boost/math/tools/big_constant.hpp

```diff
  58 | }
  59 |+ #else
  60 |+ template <typename T>
```

> Username: jzmaddock  
> Created_at: 2022-01-11 18:10:38 UTC  
> Updated_at: 2022-01-11 18:10:39 UTC  
> Url: https://github.com/boostorg/math/pull/680#discussion_r782409373  

This isn't correct: if we get here, then the type we are creating is larger than a long double, and not constructible from a string, I think the correct thing to do here is to static_assert that this is unsupported without lexical_cast.  
  
Unless I spot anything else, we'll leave this to a separate PR when I look again at unchecked_factorial.

> Username: mborland  
> Created_at: 2022-01-12 20:03:19 UTC  
> Url: https://github.com/boostorg/math/pull/680#discussion_r783404949  

If I replace this with a static assert it hits when `largest_float` = double which is easy to convert to. I added in an assertion if `std::numeric_limits<largest_float>::digits > LDBL_DIG` that standalone will need to be disabled.

> Username: jzmaddock  
> Created_at: 2022-01-13 11:59:15 UTC  
> Url: https://github.com/boostorg/math/pull/680#discussion_r783892280  

Not so fast: this should *never* be called with type double, as it is only called when std::numeric_Limits<T>::is_specialized is false (that's the first std::false_type argument amongst other conditions), *and* when T is *NOT* constructible from a string (the second false_type argument).  
  
So the intention of that overload is to handle the case where we have a multiprecision type with either a) more precision that the largest float or b) unknown (runtime) precision.  AND not constructible from a string (so not anything in Boost.Multiprecision).  
  
So mpfr_class would be an example, as would our real_concept type as that's a trivial model for the "lowest common denominator that we support.  
  
If this is really being called for type double, then something has gone badly wrong somewhere else.    
  
Do you have a test case?

> Username: mborland  
> Created_at: 2022-01-13 19:50:56 UTC  
> Url: https://github.com/boostorg/math/pull/680#discussion_r784277602  

Here is an example if I have only the `static_assert`:  
  
```  
...failed clang-darwin.compile.c++ ../../../bin.v2/libs/math/test/test_0F1_1.test/clang-darwin-13/debug/cxxstd-17-iso/link-static/threading-multi/visibility-hidden/test_0F1.o...  
...skipped <p../../../bin.v2/libs/math/test/test_0F1_1.test/clang-darwin-13/debug/cxxstd-17-iso/link-static/threading-multi/visibility-hidden>test_0F1_1 for lack of <p../../../bin.v2/libs/math/test/test_0F1_1.test/clang-darwin-13/debug/cxxstd-17-iso/link-static/threading-multi/visibility-hidden>test_0F1.o...  
...skipped <p../../../bin.v2/libs/math/test/test_0F1_1.test/clang-darwin-13/debug/cxxstd-17-iso/link-static/threading-multi/visibility-hidden>test_0F1_1.run for lack of <p../../../bin.v2/libs/math/test/test_0F1_1.test/clang-darwin-13/debug/cxxstd-17-iso/link-static/threading-multi/visibility-hidden>test_0F1_1...  
...removing outdated ../../../bin.v2/libs/math/test/test_0F1_1.test/clang-darwin-13/debug/cxxstd-17-iso/link-static/threading-multi/visibility-hidden/test_0F1_1.test  
clang-darwin.compile.c++ ../../../bin.v2/libs/math/test/test_1F1_regularized_float.test/clang-darwin-13/debug/cxxstd-17-iso/link-static/threading-multi/visibility-hidden/test_1F1_regularized.o  
In file included from test_1F1_regularized.cpp:8:  
In file included from ./test_1F1_regularized.hpp:9:  
In file included from ../../../boost/math/concepts/real_concept.hpp:29:  
../../../boost/math/tools/big_constant.hpp:63:5: error: static_assert failed due to requirement 'sizeof(double) == 1' "Type is unsupported in standalone mode. Please disable and try again."  
    static_assert(sizeof(largest_float) == 1, "Type is unsupported in standalone mode. Please disable and try again.");  
    ^             ~~~~~~~~~~~~~~~~~~~~~~~~~~  
1 error generated.  
```  
  
Looks like all the errors come propagate from testing `real_concept` where we can process `largest_float` as a double trivially unless I am seriously missing something here.

> Username: jzmaddock  
> Created_at: 2022-01-14 09:01:49 UTC  
> Updated_at: 2022-01-14 09:01:50 UTC  
> Url: https://github.com/boostorg/math/pull/680#discussion_r784669617  

Yes, your static_assert doesn't depend on a template parameter so the condition `sizeof(largest_float) == 1` is evaluated as soon as the function is parsed (and fails), not when the template is instantiated.  That's why there's no instantiation backtrace in your error messages.  
  
Try `static_assert(sizeof(T) == 0, "This should fail");`


---

## Review 15 [Commented]

> Username: jzmaddock  
> Created_at: 2022-01-11 18:13:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/680#pullrequestreview-849565844  

📁 include/boost/math/tools/lexical_cast.hpp

```diff
  23 |+ 
  24 |+     template <typename T, typename std::enable_if<std::is_same<T, float>::value, bool>::type = true>
  25 |+     inline T lexical_cast(const char* s)
```

> Username: jzmaddock  
> Created_at: 2022-01-11 18:13:23 UTC  
> Url: https://github.com/boostorg/math/pull/680#discussion_r782411356  

Are there ever any situations where we are lexical-casting to a float?  That would seem to be a mistake on our part if so?

> Username: mborland  
> Created_at: 2022-01-12 20:05:17 UTC  
> Updated_at: 2022-01-12 20:05:18 UTC  
> Url: https://github.com/boostorg/math/pull/680#discussion_r783406163  

There were a couple of these that I remember hitting in the CI so I added all of the trivial facilities in case there was a hidden case or a need down the line for some reason.

> Username: jzmaddock  
> Created_at: 2022-01-13 12:38:20 UTC  
> Url: https://github.com/boostorg/math/pull/680#discussion_r783919134  

OK, another one to look at perhaps after merging - we simply should not be doing lexical conversions on native floats.


---

## Review 16 [Commented]

> Username: jzmaddock  
> Created_at: 2022-01-11 18:20:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/680#pullrequestreview-849573564  

📁 include/boost/math/tools/polynomial_gcd.hpp

```diff
  33 | template <typename EuclideanDomain>
  53 |- inline EuclideanDomain Euclid_gcd(EuclideanDomain a, EuclideanDomain b) noexcept(std::is_arithmetic_v<EuclideanDomain>)
  34 |+ inline EuclideanDomain Euclid_gcd(EuclideanDomain a, EuclideanDomain b) noexcept(std::is_arithmetic<EuclideanDomain>::value)
```

> Username: jzmaddock  
> Created_at: 2022-01-11 18:20:53 UTC  
> Url: https://github.com/boostorg/math/pull/680#discussion_r782416597  

Can this not defer to std::gcd when available?  Euclid's method is very straightforward, but other methods (such as binary gcd) are usually faster.


---

## Review 17 [Commented]

> Username: jzmaddock  
> Created_at: 2022-01-11 18:24:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/680#pullrequestreview-849577100  

📁 include/boost/math/tr1.hpp

```diff
 110 |- #error Auto linking not supported in standalone mode.
 109 |+ #else // Standalone mode
 110 |+ #  ifdef _MSC_VER
```

> Username: jzmaddock  
> Created_at: 2022-01-11 18:24:31 UTC  
> Updated_at: 2022-01-11 18:24:32 UTC  
> Url: https://github.com/boostorg/math/pull/680#discussion_r782419113  

I don't think we need this - auto_linking is msvc only, and the user can still use the code without it, they just need to manually link.  At most a warning for msvc would be enough I think?


---

## Comment 18

> Username: jzmaddock  
> Created_at: 2022-01-11 19:12:49 UTC  
> Url: https://github.com/boostorg/math/pull/680#issuecomment-1010279048  

Serious Kudos for taking this on Matt!  There are a few minor issues which could would be good to address prior to merging, and unchecked_factorial I'll look at post merge.  Other than my comments above this all looks good though!

---

## Comment 19

> Username: mborland  
> Created_at: 2022-01-14 15:53:46 UTC  
> Url: https://github.com/boostorg/math/pull/680#issuecomment-1013244337  

@jzmaddock This should be good now; the last test is hung.

---
