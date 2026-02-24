# #205 Use the do {} while (false) idiom for BOOST_TEST_THROWS() and BOOST_TEST_NO_THROW() [Merged]

> Username: gennaroprota  
> Created at: 2025-10-09 15:29:18 UTC  
> Updated at: 2025-10-20 23:33:50 UTC  
> Merged at: 2025-10-18 13:06:01 UTC  
> Closed at: 2025-10-18 13:06:01 UTC  
> Url: https://github.com/boostorg/core/pull/205  

Reason: Common hygiene for multi-line macros. Makes things like  
  
```  
if (condition)  
    BOOST_TEST_THROWS(...);  
else  
    ...  
```  
  
work correctly. Also, it doesn't generate an empty statement (which compilers may warn about) when the user adds a semicolon (which they usually do).

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-10-09 16:41:16 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3386658611  

This is technically a breaking change because the semicolon is now required, whereas it wasn't before. But it looks like all our uses have a semicolon.  
  
The backslash at the last line shouldn't be present. We should remove it from BOOST_TEST_THROWS, instead of adding it to BOOST_TEST_NO_THROW.

---

## Comment 2

> Username: gennaroprota  
> Created_at: 2025-10-09 16:58:34 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3386730936  

> The backslash at the last line shouldn't be present. We should remove it from BOOST_TEST_THROWS, instead of adding it to BOOST_TEST_NO_THROW.  
  
I thought the convention was to terminate with a `//` comment:  
  
```  
last_line    \  
//  
```  
  
Didn't notice this hadn't been done for  `BOOST_TEST_NO_THROW()`.

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-10-09 19:01:12 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3387151672  

```  
libs\core\test\lightweight_test_test.cpp(105) : warning C4127: conditional expression is constant  
```  
Yeah :-/

---

## Comment 4

> Username: gennaroprota  
> Created_at: 2025-10-10 09:01:25 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3388973801  

Sigh. Which version of MSVC? I don't get that warning with MSVC 2022 and `/Wall`.

---

## Comment 5

> Username: pdimov  
> Created_at: 2025-10-10 09:04:11 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3388982437  

Up to msvc-12.0 according to Appveyor.

---

## Comment 6

> Username: gennaroprota  
> Created_at: 2025-10-10 09:10:19 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3389003210  

Hmm. We might try with `do {} while (0)`, which is probably more common and might be special-cased, even in old versions of MSVC.

---

## Comment 7

> Username: Lastique  
> Created_at: 2025-10-10 12:08:55 UTC  
> Updated_at: 2025-10-10 12:09:07 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3389748696  

Just add `_Pragma("warning(disable: 4127)")` to the macros. With the corresponding `push` and `pop` pragmas as well.

---

## Comment 8

> Username: pdimov  
> Created_at: 2025-10-13 18:04:28 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3398547701  

BOOST_TEST_xxx is a public macro name. Please use BOOST_LWT_DETAIL_xxx for implementation details.

---

## Comment 9

> Username: Lastique  
> Created_at: 2025-10-13 20:08:16 UTC  
> Updated_at: 2025-10-13 20:10:45 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3398910479  

One other alternative we could try is `for (;;) { x; break; }`.

---

## Comment 10

> Username: gennaroprota  
> Created_at: 2025-10-14 07:13:08 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3400422454  

That doesn't play well with the semicolon (the one after the macro invocation).

---

## Review 11 [Commented]

> Username: Lastique  
> Created_at: 2025-10-14 08:54:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/205#pullrequestreview-3334578332  

📁 include/boost/core/lightweight_test.hpp

```diff
 553 | 
 554 |+ 
 555 |+ #if !defined(BOOST_MSVC) || (BOOST_MSVC > 1900)
```

> Username: Lastique  
> Created_at: 2025-10-14 08:54:44 UTC  
> Updated_at: 2025-10-14 08:54:45 UTC  
> Url: https://github.com/boostorg/core/pull/205#discussion_r2428415679  

Minor nit: better spell the second comparison as `BOOST_MSVC >= x`, where `x` is the oldest version we know works. This saves from some SP or Update with a version that is higher than 1900 but still issues the warning.

> Username: pdimov  
> Created_at: 2025-10-14 10:41:10 UTC  
> Updated_at: 2025-10-14 10:41:11 UTC  
> Url: https://github.com/boostorg/core/pull/205#discussion_r2428696095  

> 1900 looks wrong, 1900 is msvc 14.0 unpatched. Should be >= 1900.

> Username: gennaroprota  
> Created_at: 2025-10-14 12:09:32 UTC  
> Updated_at: 2025-10-14 12:09:33 UTC  
> Url: https://github.com/boostorg/core/pull/205#discussion_r2428923877  

Note that I test if the compiler is *not* MSVC or is MSVC > 2015. I think 2015 still emits the warning for `do {} while (false)` (while MSVC 2017 doesn't), right? I might change the second test to `>= 1910` though, as Lastique suggested.  
  
```  
MSVC 2015 == 1900  
MSVC 2017 == 1910  
```

> Username: pdimov  
> Created_at: 2025-10-14 13:01:05 UTC  
> Updated_at: 2025-10-14 13:01:06 UTC  
> Url: https://github.com/boostorg/core/pull/205#discussion_r2429078923  

No, msvc-14.0 doesn't emit the warning. But if it did, >= 1910 would be the correct check.

> Username: gennaroprota  
> Created_at: 2025-10-14 13:21:45 UTC  
> Url: https://github.com/boostorg/core/pull/205#discussion_r2429160044  

Oh, sorry. Too many irons in the fire :-). Fixed, now.


---

## Comment 12

> Username: pdimov  
> Created_at: 2025-10-14 13:25:17 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3401839603  

Everything fails, probably because you're missing a semicolon here `BOOST_LWT_DETAIL_DO_WHILE_FALSE(static_cast<void>(0))`.

---

## Comment 13

> Username: pdimov  
> Created_at: 2025-10-14 13:27:14 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3401853322  

`(void)0;` is probably a more idiomatic form of a null statement, despite the C cast.

---

## Comment 14

> Username: gennaroprota  
> Created_at: 2025-10-14 13:32:51 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3401895385  

Do you prefer that? (Why?)

---

## Comment 15

> Username: pdimov  
> Created_at: 2025-10-14 14:06:32 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3402065269  

It's idiomatic because that's what `assert` typically uses.  
  
Nowadays `(void)` is also emerging as the idiomatic way to silence nodiscard warnings.

---

## Comment 16

> Username: gennaroprota  
> Created_at: 2025-10-14 14:59:14 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3402298693  

I don't have a strong opinion about that. I can change it, if you like. (About `assert()`, though, I think it's `(void)` because it's common to C.)

---

## Comment 17

> Username: pdimov  
> Created_at: 2025-10-14 15:35:16 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3402455471  

Yes please, let's go with `(void)`. I've always found `static_cast<void>` somewhat pretentious, and it does require parentheses around the expression which is also a bit jarring.

---

## Comment 18

> Username: pdimov  
> Created_at: 2025-10-15 16:23:51 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3407305742  

@Lastique @glenfe Any objections to merging this?

---

## Comment 19

> Username: Lastique  
> Created_at: 2025-10-15 17:47:28 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3407601783  

No, looks good to me.

---

## Comment 20

> Username: pdimov  
> Created_at: 2025-10-18 14:21:06 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3418465295  

This breaks at least  
  
https://github.com/boostorg/unordered/blob/4ec5cb59cd2acfd05186516f7ecede5d4440f2aa/test/unordered/transparent_tests.cpp#L1692

---

## Comment 21

> Username: pdimov  
> Created_at: 2025-10-18 14:35:52 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3418473507  

Looks like this is the only such occurrence though.

---

## Comment 22

> Username: pdimov  
> Created_at: 2025-10-18 17:19:47 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3418675305  

Nope, two more :-)  
  
https://github.com/boostorg/variant2/blob/a63b03286e3347f9d570f2b75d480f129cec8ecc/test/variant_copy_assign_throw.cpp#L48  
https://github.com/boostorg/variant2/blob/a63b03286e3347f9d570f2b75d480f129cec8ecc/test/variant_move_assign_throw.cpp#L48

---

## Comment 23

> Username: gennaroprota  
> Created_at: 2025-10-20 09:33:01 UTC  
> Updated_at: 2025-10-20 09:34:07 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3421236693  

@Lastique: That changelog entry is a bit wrong: With the previous implementation (without `do {} while (false)`), this:  
  
```  
if (condition)  
    BOOST_TEST_THROWS(...); // note the semicolon  
else  
    ...  
```  
  
was a syntax error. The warnings you hint at, instead, were for the `do {} while (false)` itself.

---

## Comment 24

> Username: Lastique  
> Created_at: 2025-10-20 16:02:13 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3422741077  

The previous definition was a direct `try`/`catch`, so if I'm not mistaken, it wasn't an error under `if` without the semicolon, was it? `BOOST_TEST_NO_THROW` also wasn't an error.  
  
BTW, given that the macros are now conditionally defined depending on `BOOST_MSVC`, does this mean that the motivating example with `if`/`else` and semicolon is still not portable and should not be used despite this PR?  
  
Anyway, if you can provide a better text in the changelog, please create a PR.

---

## Comment 25

> Username: gennaroprota  
> Created_at: 2025-10-20 16:42:23 UTC  
> Updated_at: 2025-10-20 16:44:21 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3422872588  

> The previous definition was a direct `try`/`catch`, so if I'm not mistaken, it wasn't an error under `if` without the semicolon, was it? `BOOST_TEST_NO_THROW` also wasn't an error.  
  
Yes.  
  
> BTW, given that the macros are now conditionally defined depending on `BOOST_MSVC`, does this mean that the motivating example with `if`/`else` and semicolon is still not portable and should not be used despite this PR?  
  
Yes, it can be used only if MSVC < 2015 is of no concern :-(. That MSVC warning ruined everything. I wish I could withdraw this PR.

---

## Comment 26

> Username: pdimov  
> Created_at: 2025-10-20 23:33:50 UTC  
> Url: https://github.com/boostorg/core/pull/205#issuecomment-3424110453  

Fixed in https://github.com/boostorg/core/commit/7b2c714b5562c515206383d9b12f00c02a7b63f1.

---
