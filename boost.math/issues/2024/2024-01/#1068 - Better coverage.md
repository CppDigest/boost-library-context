# #1068 - Better coverage? [Open]

> Username: jzmaddock  
> Created at: 2024-01-21 13:21:21 UTC  
> Updated at: 2024-02-17 10:18:35 UTC  
> Url: https://github.com/boostorg/math/issues/1068  

I've just briefly looked at the codecov reports (many thanks for doing this @mborland !) to see what needs improving, and I'm seeing a lot of false flags, for example in https://app.codecov.io/gh/boostorg/math/pull/1067/blob/include/boost/math/special_functions/lanczos.hpp line 870 only the final initializer in the list is marked as covered, even though they must have all been executed if one has!  Is there anything we can do about this?

---

## Comment 1

> Username: ckormanyos  
> Created at: 2024-01-21 13:31:48 UTC  
> Updated at: 2024-01-21 13:32:24 UTC  
> Url: https://github.com/boostorg/math/issues/1068#issuecomment-1902630745  

Numerous `return` lines are, in fact, marked as covered. Notwithstanding that, however, the tool seems to be experiencing problems with `BOOST_MATH_BIG_CONSTANT`. We could see what happens on some explicit local tests as time allows.  
  
Personally, I have found coverage on highly-templated C++ code to be somewhat challenging.  
  
I also recommend to cut the optimization level way down to `-O1` or even `-O0` when running gcov/lcov. I have not looked at the make system. But that is also something can be tuned. I almost never change code for the sake of covering templates, but try to tune the environment or code-annotations to better reflect the honest situation.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2024-01-21 13:37:33 UTC  
> Updated at: 2024-01-21 13:49:12 UTC  
> Url: https://github.com/boostorg/math/issues/1068#issuecomment-1902632263  

That being said, my personal experience is that it can take a comparable amount of time to attain $100\\%$ line coverage as it took or takes to write the code in the first place. I find that testing effort is often underestimated on the road to quality. Somewhere a while back I mentioned increasing line coverage very briefly and suspect we need a dedicated effort to reach (via specially contrived test-cases) more hard-to-reach lines.  
  
In the case mentioned here, however, I do believe that `lcov` might have representation problems with some of those big macros.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2024-01-21 16:35:18 UTC  
> Url: https://github.com/boostorg/math/issues/1068#issuecomment-1902689038  

It took a bit of detective work, but yes, it's using lcov, and lcov has exclusion macros we can use.  I did spot a few genuine cases not covered while browsing through, so maybe I'll see what I can squash.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2024-01-21 16:58:45 UTC  
> Updated at: 2024-01-21 17:00:01 UTC  
> Url: https://github.com/boostorg/math/issues/1068#issuecomment-1902695348  

Sometimes, well we will ultimately just _know_ _better_ than `lcov`, which my opinion is, can't fully handle modern C++, but sure gives a good indication of what's going on.  
  
Then I use (in justified cases - whatever that is decided to mean for our projects)  
  
> lcov has exclusion macros we can use  
  
For multipple lines:  
```  
// LCOV_EXCL_START  
my_uncovered_line();  
my_other_uncovered_line();  
// LCOV_EXCL_STOP  
```  
  
Or for single line(s)  
```  
my_uncovered_line(); // LCOV_EXCL_LINE  
my_other_uncovered_line(); // LCOV_EXCL_LINE  
```

---

## Comment 5

> Username: ckormanyos  
> Created at: 2024-02-17 10:18:33 UTC  
> Url: https://github.com/boostorg/math/issues/1068#issuecomment-1949925894  

See also:  
- #1084 `tgamma()`/`lgamma()` edge cases for $250$ decimal digits.  
- #1090 achieve rudimentary octonion coverage.  
- #1096 actually run (in addition to instantiate) the code in [`instantiate.hpp`](https://github.com/boostorg/math/blob/instantiation_runners/test/compile_test/instantiate.hpp).
