# #33 fixes parse of -1e-308 [Closed]

> Username: madmongo1  
> Created at: 2020-01-10 10:56:57 UTC  
> Updated at: 2022-12-22 09:34:35 UTC  
> Closed at: 2020-01-14 22:32:08 UTC  
> Url: https://github.com/boostorg/json/pull/33  

error was in `finish()` in `exp3` state

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-10 11:28:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341105752  

📁 include/boost/json/detail/impl/number.ipp

```diff
  95 |+     if (exp < -308 || exp > 308)
  96 |+     {
  97 |+         return std::pow(10.0, exp);
```

> Username: vinniefalco  
> Created_at: 2020-01-10 11:28:44 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365191583  

idk about this

> Username: madmongo1  
> Created_at: 2020-01-10 13:02:42 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365223305  

I'll get to it. I wanted to prevent the assert from happening

> Username: vinniefalco  
> Created_at: 2020-01-13 15:18:37 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365859798  

Do we still need this check? We need to add tests for the exponent edge cases

> Username: madmongo1  
> Created_at: 2020-01-13 15:54:26 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365879836  

we could just check for "more than 3 digits" and then rely on the finalize to detect the over/underflow. I think i'd prefer that...  
  
oh wait. What if the input is 0.[ten thousand zeroes]1e10000 ?  
there's no reason we shouldn't parse this correctly.  
  
It would be trivial to compute the exponent limits on entry into this state

> Username: madmongo1  
> Created_at: 2020-01-13 16:37:10 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365904031  

In my view the check is superfluous. it's more expensive to perform the check than to fail late.

> Username: vinniefalco  
> Created_at: 2020-01-13 17:36:14 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365933821  

In general I think it is good to fail as soon as we know we have an invalid input. So if we get 309 as the exponent, we can fail right there. I'm not happy about having a potential call to `std::pow`, especially when we know that the exponent is out of range.

> Username: madmongo1  
> Created_at: 2020-01-13 21:30:22 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r366038592  

0.0000000001e309 is actually 1e289. stod will parse this correctly.


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-10 11:59:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341119258  

📁 include/boost/json/detail/impl/number.ipp

```diff
 292 |                     }
 293 |-                     ++p;
 293 |+                     ++p; // consume digit
```

> Username: vinniefalco  
> Created_at: 2020-01-10 11:59:30 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365202093  

Another way to spell "consume digit" is `++p`

> Username: vinniefalco  
> Created_at: 2020-01-13 15:23:18 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365862342  

The comment is extraneous, `++p` already means "consume digit"


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-10 12:00:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341119842  

📁 include/boost/json/detail/impl/number.ipp

```diff
 143 |         dig_ = 1;
 144 |+         // a leading non-zero digit is always significant
 145 |+         sig_ = 1;
```

> Username: vinniefalco  
> Created_at: 2020-01-10 12:00:51 UTC  
> Updated_at: 2020-01-14 21:43:31 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365202483  

extraneous comment


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-10 12:01:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341119952  

📁 include/boost/json/detail/impl/number.ipp

```diff
 354 |-                     ++dig_;
 356 |+                     ++sig_; // increase number of significant digits in mantissa
 357 |+                     ++dig_; // increase total number of digits in mantissa
```

> Username: vinniefalco  
> Created_at: 2020-01-10 12:01:05 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365202572  

extraneous comments


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-10 12:02:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341120525  

📁 test/_detail_number.cpp

```diff
 550 |-             "00000000000000000000000000000000000000000000000000"
 551 |-             "00000000000000000000000000000000000000000000000000" // 500 zeroes
 456 |+         for (int i = 0 ; i < 2 ; ++i)
```

> Username: vinniefalco  
> Created_at: 2020-01-10 12:02:24 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365203022  

FYI This is valid C++  
```  
for(auto b : {true, false})  
```

> Username: madmongo1  
> Created_at: 2020-01-10 13:03:54 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365223772  

ah, nice.

> Username: madmongo1  
> Created_at: 2020-01-13 16:00:27 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365883373  

no longer needed. we're not parsing accurately atm


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-10 12:03:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341121092  

📁 test/_detail_number.cpp

```diff
 438 |+                 std::endl;
 439 |+         }
 440 |+         else if (pedantic and !BOOST_TEST(same))
```

> Username: vinniefalco  
> Created_at: 2020-01-10 12:03:44 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365203446  

`&&`

> Username: madmongo1  
> Created_at: 2020-01-10 13:04:10 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365223869  

oops. old habits...

> Username: madmongo1  
> Created_at: 2020-01-13 16:00:43 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365883534  

gone now


---

## Comment 7

> Username: vinniefalco  
> Created_at: 2020-01-10 12:04:58 UTC  
> Url: https://github.com/boostorg/json/pull/33#issuecomment-573009883  

The first line of a commit message should be short, certainly not with a number having 500 zeroes...  
  
There's no need to quote the number in the commit message, since it is already visible in the test.

---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-10 12:05:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341121877  

📁 CMakeLists.txt

```diff
  88 |             Boost::system
  89 |     )
  90 |+     option(BUILD_TESTING "Build the tests" ON)
```

> Username: vinniefalco  
> Created_at: 2020-01-10 12:05:40 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365204046  

Commit message should read  
"Add cmake option to build tests and examples"

> Username: vinniefalco  
> Created_at: 2020-01-13 15:18:17 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365859618  

Outdated review comment, disregard


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-10 12:07:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341122488  

📁 include/boost/json/detail/impl/number.ipp

```diff
 313 |+                     // if decimal point has not yet been seen, assume it's here
 314 |+                     // since 100e0 is equivalent to 100.0e0
 315 |+                     if (pos_ < 0) pos_ = dig_;
```

> Username: vinniefalco  
> Created_at: 2020-01-10 12:07:06 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365204505  

The statement has to be on its own line  
```  
if (pos_ < 0)  
    pos_ = dig_;  
```  
for two reasons: to be able to set a breakpoint, and to distinguish the lines in coverage reports.


---

## Comment 10

> Username: vinniefalco  
> Created_at: 2020-01-13 13:30:22 UTC  
> Url: https://github.com/boostorg/json/pull/33#issuecomment-573663925  

This should be squashed down into just one commit

---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 15:21:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341919804  

📁 include/boost/json/detail/impl/number.ipp

```diff
 225 |             pos_ = 0;
 218 |-             st_ = state::zeroes;
 226 |+             st_ = state::mant_frac_1_digit;
```

> Username: vinniefalco  
> Created_at: 2020-01-13 15:21:04 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365861123  

Suggested: `state::mantf`

> Username: madmongo1  
> Created_at: 2020-01-13 16:03:18 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365885028  

may I at least comment the purpose of the state?


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 15:21:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341919997  

📁 include/boost/json/detail/impl/number.ipp

```diff
 240 | 
 233 |-     //-----------------------------------
 241 |+     // 1*digit
```

> Username: vinniefalco  
> Created_at: 2020-01-13 15:21:23 UTC  
> Updated_at: 2020-01-14 21:43:31 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365861271  

Good comment


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 15:22:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341920642  

📁 include/boost/json/detail/impl/number.ipp

```diff
 258 |+                     st_ = state::mantn;
 259 |+                 else
 260 |+                     st_ = state::mant;
```

> Username: vinniefalco  
> Created_at: 2020-01-13 15:22:14 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365861761  

```  
else if(neg_)  
{  
    st_ = state::mantn;  
}  
else  
{  
    st_ = state::mant;  
```


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 15:23:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341921297  

📁 include/boost/json/detail/impl/number.ipp

```diff
 266 |+             ec = error::expected_fraction;
 267 |+             goto finish;
 268 |+         }
```

> Username: vinniefalco  
> Created_at: 2020-01-13 15:23:04 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365862203  

No need for `else` since we used `goto` in the `if`


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 15:23:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341921665  

📁 include/boost/json/detail/impl/number.ipp

```diff
 289 |-                     ++dig_;
 325 |+                     ++dig_; // increase digit count
 326 |+                     ++sig_; // increase significant digit count
```

> Username: vinniefalco  
> Created_at: 2020-01-13 15:23:34 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365862478  

Extraneous comments


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 15:24:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341922069  

📁 include/boost/json/detail/impl/number.ipp

```diff
 340 |                 {
 341 |+                     // if decimal point has not yet been seen, assume it's here
 342 |+                     // since 100e0 is equivalent to 100.0e0
```

> Username: vinniefalco  
> Created_at: 2020-01-13 15:24:05 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365862809  

Comment could be simplified to `// Treat 'E' as '.'`


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 15:24:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341922623  

📁 include/boost/json/detail/impl/number.ipp

```diff
 401 |+                     // then the decimal point must be assumed to be at the position of
 402 |+                     // the [eE]
 403 |+                     // e.g. 100e0 is equivalent to 100.0
```

> Username: vinniefalco  
> Created_at: 2020-01-13 15:24:50 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365863217  

LOL what is this wall of text? No one will want to read that, consider `// Treat 'E' as '.'`

> Username: madmongo1  
> Created_at: 2020-01-13 16:16:07 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365891895  

I'd want to read it :)  
  
Resolved with `// 'E' implies '.' if not already encountered`


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 15:26:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341923642  

📁 include/boost/json/detail/impl/number.ipp

```diff
 451 |+                 // we must assume it's at the [eE]
 452 |+                 // because 100e0 is equivalent to 100.0e0
 453 |+                 if (pos_ < 0) pos_ = dig_;
```

> Username: vinniefalco  
> Created_at: 2020-01-13 15:26:10 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365863975  

The condition and statements need to be on separate lines for coverage and breakpoints


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 15:27:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341924599  

📁 include/boost/json/detail/impl/number.ipp

```diff
 702 |+         {
 703 |+             // this indicates that the decimal point preceeds any significant digits
 704 |+             // which means the mantissa has a magnitude < 1.0
```

> Username: vinniefalco  
> Created_at: 2020-01-13 15:27:27 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365864708  

Consider just `// mantissa < 1`

> Username: madmongo1  
> Created_at: 2020-01-13 16:18:38 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365893307  

will change to `// |mantissa| < 1.0` as this succinctly contains 2 more data:  
  
1. we are considering the magnitude of the mantissa  
2. the magnitude of the mantissa is a real number


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 15:28:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341925224  

📁 include/boost/json/detail/number.hpp

```diff
  40 |     {
  41 |         init,   init0,  init1,
  42 |+         mant_frac_1_digit,      // mantissa: after decimal there must be a digit
```

> Username: vinniefalco  
> Created_at: 2020-01-13 15:28:18 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365865195  

`mantf` would be more in line with the style, and there's no need for the comment here since there is one in the case label (the `1*digit`)


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 15:32:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341928619  

📁 test/_detail_number.cpp

```diff
  17 | #include "test_suite.hpp"
  18 | 
  19 |+ #define BOOST_JSON_DETAIL_NUMBER_DEMAND_PERFECT_DOUBLE_CONVERSION 0
```

> Username: vinniefalco  
> Created_at: 2020-01-13 15:32:48 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365867679  

Not sterile, and this macro is only visible in tests so you can just use `#define ACCURATE_CONVERSION` or something like that


---

## Review 22 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 15:33:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341928980  

📁 test/_detail_number.cpp

```diff
 372 |         check_bad("-x");
 373 |+         check_bad("2.e+3"); //must be at least 1 digit after mantissa decimal
 374 |+         check_bad("-2.e+3"); //must be at least 1 digit after mantissa decimal
```

> Username: vinniefalco  
> Created_at: 2020-01-13 15:33:17 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365867926  

These comments are all extraneous, `check_bad` subsumes them


---

## Review 23 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 15:33:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-341929422  

📁 test/_detail_number.cpp

```diff
 558 |-             "00000000000000000000000000000000000000000000000000" // 500 zeroes
 559 |-             "1e600", f_boost{} );
 660 |+         check_bad("-2.e+3"); //must be at least 1 digit after mantissa decimal
```

> Username: vinniefalco  
> Created_at: 2020-01-13 15:33:52 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365868252  

extraneous comment


---

## Review 24 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 17:37:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-342017251  

📁 include/boost/json/detail/impl/number.ipp

```diff
 233 |-     //-----------------------------------
 240 |+     // 1*digit
 241 |+     case state::mantf:
```

> Username: vinniefalco  
> Created_at: 2020-01-13 17:37:26 UTC  
> Updated_at: 2020-01-14 21:43:31 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365934391  

if you really want to make up for the comment removed from the header, you could write  
```  
// 1*digit  
case state::mantf  
{  
    // require a valid character after the decimal point  
```  
Although it isn't strictly needed since the BNF already explains it.


---

## Review 25 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 17:40:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-342019081  

📁 include/boost/json/detail/impl/number.ipp

```diff
 337 |                 if(*p == 'e' || *p == 'E')
 338 |                 {
 339 |+                     // 'E' implies '.' if not already encountered
```

> Username: vinniefalco  
> Created_at: 2020-01-13 17:40:27 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365935829  

"if not already encountered" is superfluous, it is already spelled `if (pos_ < 0)`. This comment could be more succinct if written as `// treat 'E' as '.'`


---

## Review 26 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 17:41:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-342019408  

📁 include/boost/json/detail/impl/number.ipp

```diff
 396 |                 if(*p == 'e' || *p == 'E')
 397 |                 {
 398 |+                     // 'E' implies '.' if not already encountered
```

> Username: vinniefalco  
> Created_at: 2020-01-13 17:40:59 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365936077  

`// treat 'E' as '.'`


---

## Review 27 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 17:41:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-342019592  

📁 include/boost/json/detail/impl/number.ipp

```diff
 443 |             if(*p == 'e' || *p == 'E')
 444 |             {
 445 |+                 // 'E' implies '.' if not already encountered
```

> Username: vinniefalco  
> Created_at: 2020-01-13 17:41:16 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365936234  

`// treat 'E' as '.'`


---

## Review 28 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 17:45:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-342022164  

📁 include/boost/json/detail/impl/number.ipp

```diff
 695 |+         if (pos_ == 0)
 696 |+         {
 697 |+             // |mantissa| < 1.0
```

> Username: vinniefalco  
> Created_at: 2020-01-13 17:45:20 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365938188  

I like this comment, and it can be even better written as `abs(mantissa) < 1`


---

## Review 29 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 17:46:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-342022626  

📁 include/boost/json/detail/impl/number.ipp

```diff
 702 |+         else
 703 |+         {
 704 |+             // mantissa >= 1.0
```

> Username: vinniefalco  
> Created_at: 2020-01-13 17:46:07 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365938566  

is this `abs(mantissa) >= 1` ?


---

## Review 30 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 17:47:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-342023470  

📁 include/boost/json/detail/number.hpp

```diff
  51 |-     short off_; // mantissa's contribution to exponent
  52 |     short pos_; // position of decimal point
  53 |     short sig_; // significant digits in mantissa
```

> Username: vinniefalco  
> Created_at: 2020-01-13 17:47:30 UTC  
> Updated_at: 2020-01-14 21:43:31 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365939269  

good comments


---

## Review 31 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 17:54:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-342027677  

📁 test/_detail_number.cpp

```diff
  90 |+         try
  91 |+         {
  92 |+             constexpr auto digits = std::numeric_limits<double>::max_digits10;
```

> Username: vinniefalco  
> Created_at: 2020-01-13 17:54:30 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365942690  

what is `constexpr` buying us here and why even have `digits` at all? i.e. why not  
```  
std::setprecision(std::numeric_limits<double>::max_digits10)  
```  
?  
  
When we name a thing we elevate its importance, we should not elevate things needlessly.

> Username: madmongo1  
> Created_at: 2020-01-14 12:01:13 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r366299910  

The reason I do this:  
Spelling out concepts one per line is to me the same as your preference for formatting code in terms of one piece of entropy per line.  
Naming a temporary variable like this is a zero-cost abstraction. `constexpr` is a signal that this is a 'number concept' as opposed to an actual value object.  
  
Nevertheless, your code, your rules. Will change.


---

## Review 32 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-13 19:10:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-342072408  

📁 test/basic_parser.cpp

```diff
 483 |             if(! BOOST_TEST(! ec))
 484 |+             {
 485 |+                 ::test_suite::log_type() << "    failed to parse: " << s;
```

> Username: vinniefalco  
> Created_at: 2020-01-13 19:10:05 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r365976736  

Not a fan of this why didn't we keep it using the `log` data member?

> Username: madmongo1  
> Created_at: 2020-01-13 21:28:20 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r366037637  

hmm it failed to compile for me for some reason. Will recheck

> Username: vinniefalco  
> Created_at: 2020-01-13 22:50:20 UTC  
> Updated_at: 2020-01-14 12:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r366071190  

the lambda needs to capture `this`, and functions which use the log or call functions (or lambdas) that use the log cannot be `static`


---

## Comment 33

> Username: vinniefalco  
> Created_at: 2020-01-14 00:23:52 UTC  
> Url: https://github.com/boostorg/json/pull/33#issuecomment-573939011  

I think we should merge the `mant` and `mantn` states as you proposed today

---

## Review 34 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-14 21:29:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/33#pullrequestreview-342851036  

📁 include/boost/json/detail/impl/number.ipp

```diff
 698 |+             auto start = dig_ - sig_;
 699 |+             auto exponent_adjust = -start - 1;
 700 |+             exp_ += exponent_adjust;
```

> Username: vinniefalco  
> Created_at: 2020-01-14 21:29:48 UTC  
> Updated_at: 2020-01-14 21:43:31 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r366582002  

despite all the naming fanfare it produces warnings:  
```  
warning C4244: '+=': conversion from 'int' to 'short', possible loss of data  
```

> Username: vinniefalco  
> Created_at: 2020-01-14 21:31:54 UTC  
> Updated_at: 2020-01-14 21:43:31 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r366582878  

When you take away the unnecessary local variables and simplify the formulas, the interesting relationship between the values becomes visible:  
```  
        if (pos_ == 0)  
        {  
            // abs(mantissa) < 1  
            exp_ = static_cast<short>(  
                exp_ + sig_ - dig_ - 1);  
        }  
        else  
        {  
            // abs(mantissa) >= 1  
            exp_ = static_cast<short>(  
                exp_ + pos_ - sig_);  
        }  
```

> Username: ofenloch  
> Created_at: 2022-12-21 21:21:05 UTC  
> Updated_at: 2022-12-21 21:41:33 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r1054846917  

I don't know if my problem is related to your discussion. I have a simple Catch2 test for boost.json 1.75.0 that fails because float/double values are not written/read properly:  
  
```cpp  
TEST_CASE("boost::json")  
{  
    // There seems to be an issue with number formatting.  
  
    // create a boost::json::value  
    boost::json::value jv_original = {  
        {"pi", 3.14159265359},  
        {"e", 2.71828182846}};  
  
    // serialize the boost::json::value to a string  
    std::string s{boost::json::serialize(jv_original)};  
    // and write the string to a file  
    std::string file_name = std::tmpnam(nullptr);  
    file_name += ".json";  
    std::ofstream ofs(file_name.c_str());  
    ofs << s;  
    ofs.close();  
  
    // now read the file into a string  
    std::ifstream ifs(file_name);  
    std::string content((std::istreambuf_iterator<char>(ifs)),  
                        (std::istreambuf_iterator<char>()));  
    ifs.close();  
    std::remove(file_name);  
  
    // and create another boost::json::value from that string  
    boost::system::error_code parseErr;  
    boost::json::value jv_unserialized = boost::json::parse(content, parseErr);  
  
    // compare both boost::json::value objects  
    CHECK(jv_original == jv_unserialized);  
  
} // TEST_CASE("boost::json")  
```  
  
The error message from Catch2 is  
```bash  
[ctest]   CHECK( jv_original == jv_unserialized )  
[ctest] with expansion:  
[ctest]   {"pi":3.14159265359E0,"e":2.71828182846E0}  
[ctest]   ==  
[ctest]   {"pi":3.1415926535899996E0,"e":2.71828182846E0}  
```

> Username: grisumbras  
> Created_at: 2022-12-22 08:36:14 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r1055212435  

Please test against recent Boost version. I can't reprodcue your issue locally on 1.81.0

> Username: ofenloch  
> Created_at: 2022-12-22 09:27:58 UTC  
> Updated_at: 2022-12-22 09:34:35 UTC  
> Url: https://github.com/boostorg/json/pull/33#discussion_r1055258619  

Thanks for your answer, @grisumbras.  
  
You're right. I built and installed Boost 1.81.0 (still running on Ubuntu 20.04) and the failure is gone.  
  
Ignore the things I wrote below. I used the pretty_print code from the examples to serialize my object. When I use boost::json::value::serialize everything's fine.  
  
Thanks for your input.  
  
  
> However, I use Boost.JSON 1.81.0 in one of my classes and still have the same problem with this value in a JSON file  
>   
> ```json  
> "latitude": 79.824228,  
> ```  
>   
> Putting this value in the code above results in success. But my compare(lhs, rhs) method says:  
>   
> ```bash  
> [ctest] values of "latitude" differ:  
> [ctest]   lhs 7.9824228E1  
> [ctest]   rhs 7.98242E1  
> ```  
>   
> I'll investigate further ...  
>


---
