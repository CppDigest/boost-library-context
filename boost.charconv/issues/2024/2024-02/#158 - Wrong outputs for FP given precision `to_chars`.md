# #158 - Wrong outputs for FP given precision `to_chars` [Closed]

> Username: jk-jeon  
> Created at: 2024-02-07 00:54:01 UTC  
> Updated at: 2024-02-21 12:38:31 UTC  
> Closed at: 2024-02-21 12:38:31 UTC  
> Url: https://github.com/boostorg/charconv/issues/158  

Hi, I think the way Boost.Charconv deals with FP `to_chars` is wrong when the precision is given.  
  
Let's say we do  
  
```  
char buffer[60];  
double d = 1e-15;  
auto res = boost::charconv::to_chars(buffer, buffer + sizeof(buffer), d,  
                     boost::charconv::chars_format::scientific, 50);  
*res.ptr = '\0';  
```  
  
The supposed output is the 51 decimal digits of `1e-15` with rounding, in scientific format, which is: `1.00000000000000007770539987666107923830718560119502e-15`.   
  
But it seems Boost.Charconv produces `0.000000000000001`, which is neither in scientific form nor of 51 digits.  
  
On the other hand, if we do `chars_format::general` instead, then we get the same output `0.000000000000001` while it's supposed to be `1.0000000000000000777053998766610792383071856011950e-15` (i.e., one less digit due to the inconsistency in how digits are counted between scientific vs general. I'm not sure if the trailing zero in this case is needed/must be removed/doesn't matter... MS STL implementation seems to remove it, but I'm not sure what's the exact requirement.)  
  
Finally, if we do `chars_format::fixed` instead, then we again get the same output `0.000000000000001` while it's supposed to be `0.00000000000000100000000000000007770539987666107924` (i.e., 50 digits after the decimal dot).  
  
I looked into the implementation and it seems Boost.Charconv invokes `dragonbox::to_decimal` if the input is in `[1e-16,1e16)`. I am not so sure what was the intention of doing these comparisons, but as far as I understand `dragonbox::to_decimal` in general will not be enough when the precision is given, especially when it's absurdly large like the case above. (It would be enough when precision is small and the input is normal I think, though.)  
  
The route invoking `floff` also looks wrong. For instance, let `d = 1e-17` instead of `1e-15` and repeat the above three experiments. For the case of `chars_format::scientific` it works correctly. For the case of `chars_format::general`, it should produce one less digit again due to different ways of interpreting `precision` for these two formats, but Boost.Charconv produces identical outputs. For the case of `chars_format::fixed`, the output must be in the fixed-point form (`0.00000000000000001000000000000000071542424054621925`) with 50 digits after the decimal dot, but the Boost.Charconv's output is again identical to the case of `chars_format::scientific`.  
  
Note that `floff` does not provide an option for fixed-point output, so you can't really treat it as a complete blackbox. I think therefore you have four options at this moment:  
  
1. Just use the reference implementation of Ryu-printf like other std implementations out there do.  
2. Still use `floff` as a blackbox but manipulate the precision and the resulting string.  
3. Look into how `floff` internally works and tweak it into an implementation of fixed-point formatting.  
4. Look for something else, like ones using Dragon4 but without allocation, instead using stack-allocated memory.  
  
I somehow had an impression that you already did the third option... I don't know why. Thinking about it again it sounds like the least manageable option. In my opinion maybe the most efficient option is 1, whose downside is a huge table.

---

## Comment 1

> Username: mborland  
> Created at: 2024-02-07 07:22:15 UTC  
> Url: https://github.com/boostorg/charconv/issues/158#issuecomment-1931428188  

>   
> I looked into the implementation and it seems Boost.Charconv invokes `dragonbox::to_decimal` if the input is in `[1e-16,1e16)`. I am not so sure what was the intention of doing these comparisons, but as far as I understand `dragonbox::to_decimal` in general will not be enough when the precision is given, especially when it's absurdly large like the case above. (It would be enough when precision is small and the input is normal I think, though.)  
>   
  
The intention here is that dragonbox, Ryu, etc. all only give scientific formatting. In that range for doubles with general formatting the output should be in fixed form to give the shortest representation, unless the precision is specified.  
  
>   
> Note that `floff` does not provide an option for fixed-point output, so you can't really treat it as a complete blackbox. I think therefore you have four options at this moment:  
>   
> 1. Just use the reference implementation of Ryu-printf like other std implementations out there do.  
> 2. Still use `floff` as a blackbox but manipulate the precision and the resulting string.  
> 3. Look into how `floff` internally works and tweak it into an implementation of fixed-point formatting.  
> 4. Look for something else, like ones using Dragon4 but without allocation, instead using stack-allocated memory.  
>   
> I somehow had an impression that you already did the third option... I don't know why. Thinking about it again it sounds like the least manageable option. In my opinion maybe the most efficient option is 1, whose downside is a huge table.  
  
So far I have a combination of 2 and 3. The format is passed into floff to manipulate some of the output in Phase 3, but clearly the fixed formatting can be better. I'll look into this more since I would rather keep the performance of floff. Since this is a compiled library I am not overly concerned about the tables if it comes to that.  
  
Thanks for looking into this.

---

## Comment 2

> Username: jk-jeon  
> Created at: 2024-02-07 08:26:04 UTC  
> Updated at: 2024-02-07 08:29:04 UTC  
> Url: https://github.com/boostorg/charconv/issues/158#issuecomment-1931514671  

>The intention here is that dragonbox, Ryu, etc. all only give scientific formatting. In that range for doubles with general formatting the output should be in fixed form to give the shortest representation, unless the precision is specified.  
  
I see. I didn't meticulously think about the exact numerics and such, but at first glance this line seems to match your explanation:  
  
https://github.com/boostorg/charconv/blob/2e74155a362860ef84cb26cf773820e78f428020/src/to_chars_float_impl.hpp#L563  
  
But this line looks sort of suspicious to me:  
  
https://github.com/boostorg/charconv/blob/2e74155a362860ef84cb26cf773820e78f428020/src/to_chars_float_impl.hpp#L591  
  
Like I said I think `dragonbox::to_decimal` would be not very useful when the precision is given. To be more precise, when the given precision is small then you could maybe utilize the return value of `dragonbox::to_decimal`, but what `floff` internally does for small precision is more or less just exactly that except that it doesn't do needless shortest representation searching and instead it implements the rounding logic properly.  
  
>So far I have a combination of 2 and 3. The format is passed into floff to manipulate some of the output in Phase 3, but clearly the fixed formatting can be better.  
  
I briefly looked at what you did and now it kinda makes sense to me. I guess what's needed is a precision adjustment for the `chars_format::fixed` case, based on the exact position of the first nonzero digit. I don't know the exact requirement for `chars_format::general`, so I don't know what exactly needs to be done for that case, other than that the precision must be adjusted by 1 if scientific format is chosen.

---

## Comment 3

> Username: mborland  
> Created at: 2024-02-07 08:33:05 UTC  
> Url: https://github.com/boostorg/charconv/issues/158#issuecomment-1931525270  

> > The intention here is that dragonbox, Ryu, etc. all only give scientific formatting. In that range for doubles with general formatting the output should be in fixed form to give the shortest representation, unless the precision is specified.  
>   
> I see. I didn't meticulously think about the exact numerics and such, but at first glance this line seems to match your explanation:  
>   
> https://github.com/boostorg/charconv/blob/2e74155a362860ef84cb26cf773820e78f428020/src/to_chars_float_impl.hpp#L563  
>   
> But this line looks sort of suspicious to me:  
>   
> https://github.com/boostorg/charconv/blob/2e74155a362860ef84cb26cf773820e78f428020/src/to_chars_float_impl.hpp#L591  
>   
  
Yes that line needs to go away and floff should be the only thing called in the precision specified path.  
  
> Like I said I think `dragonbox::to_decimal` would be not very useful when the precision is given. To be more precise, when the given precision is small then you could maybe utilize the return value of `dragonbox::to_decimal`, but what `floff` internally does for small precision is more or less just exactly that except that it doesn't do needless shortest representation searching and instead it implements the rounding logic properly.  
>   
> > So far I have a combination of 2 and 3. The format is passed into floff to manipulate some of the output in Phase 3, but clearly the fixed formatting can be better.  
>   
> I briefly looked at what you did and now it kinda makes sense to me. I guess what's needed is a precision adjustment for the `chars_format::fixed` case, based on the exact position of the first nonzero digit. I don't know the exact requirement for `chars_format::general`, so I don't know what exactly needs to be done for that case, other than that the precision must be adjusted by 1 if scientific format is chosen.  
>   
  
I think instead of printing the exponent I should be able to `memset` the exponents worth of leading zeros in the fixed case.  
  
> By the way, congrat for the acceptance, and thanks for all your hard work!  
  
Thank you for all the help along the way!

---

## Comment 4

> Username: jk-jeon  
> Created at: 2024-02-07 08:37:55 UTC  
> Url: https://github.com/boostorg/charconv/issues/158#issuecomment-1931532969  

>I think instead of printing the exponent I should be able to memset the exponents worth of leading zeros in the fixed case.  
  
Yeah and the precision needs to be adjusted as well because the number of digits to be printed (counted from the first nonzero digit, which is what `floff` cares about) is different from the scientific case.

---

## Comment 5

> Username: mborland  
> Created at: 2024-02-13 08:07:29 UTC  
> Url: https://github.com/boostorg/charconv/issues/158#issuecomment-1940674569  

I believe the floff portion is now working but for 80 and 128 bit long doubles we see similar issues out of Ryu.  
  
Example:  
80-bit long double 1e15L  
GCC-13: 1000000000000000.00000000000000000000000000000000000000000000000000  
Charconv: 10000000000000000000000000000000000000000000000000  
  
80-bit long double 1e17L  
GCC-13: 100000000000000000.00000000000000000000000000000000000000000000000000  
Charconv: 10000000000000000000000000000000000000000000000000  
  
Scientific format with specified precision matches.

---

## Comment 6

> Username: jk-jeon  
> Created at: 2024-02-13 10:18:31 UTC  
> Url: https://github.com/boostorg/charconv/issues/158#issuecomment-1941077996  

Hi Matt,  
  
I tested with `double d = 1e-17` and there seems to be still some issues.  
  
1. For `chars_format::scientific`,  
  
It seems now it prints one less digits than needed. The way `chars_format::scientific` and `chars_format::general` interpret the precision argument are different, since the former counts the number of digits after the decimal dot, while the latter counts the number of all printed decimal mantissa digits, i.e., the latter should print one less digit than the former when the same `precision` argument is given (because it includes the first digit when counting the precision, while the former doesn't).  
  
2. For `chars_format::general`,  
  
The way `fixed` vs `scientifc` is chosen seems wrong to me. As far as I know the rule follows that of `%g` of `std::printf`. According to https://en.cppreference.com/w/cpp/io/c/fprintf, `fixed` is chosen if (1) for the number of digits to be printed is strictly larger than the decimal exponent, and (2) the decimal exponent is at least `-4`, which basically means that:  
  
- When the exponent is nonnegative, every digit of the integer part is to be printed, and  
- When the exponent is negative, the face value of the printed string must be at least `0.0001`.  
  
And `scientific` is chosen otherwise. In particular, anything smaller than `0.0001` should be printed in `scientific` format, but the way Boost.Charconv currently works doesn't seem to agree with that.  
  
3. For `chars_format::fixed`,  
  
The number of digits printed seems correct, but the rounding logic seems wrong. I believe the relevant line is here:  
  
https://github.com/boostorg/charconv/blob/0dac3d5dd4da8ee1760d9363aa5986178d8f582f/include/boost/charconv/detail/dragonbox/floff.hpp#L3844  
  
The round-up should be done according to "round-to-nearest, tie-to-even" rule, which means:  
  
- Either the digit-at-rounding is strictly larger than 5, or  
- Is equal to 5 and:  
  - Either there are further nonzero digits after the digit-at-rounding, or  
  - there is no further nonzero digit but the last digit before the digit-at-rounding is odd.  
  
Also, if rounded-up digit were `9`, then the addition needs to propagate back to earlier digits as well. At the worst case this may even change the exponent, e.g. like when `99....99` is rounded to `100....00`. The proper handling of all these is really annoying, and I think maybe the easiest way to do this correctly is to let `floff` to do it. That is, I think you need to adjust upfront the precision parameter that `floff` uses (either `precision` or `remaining_digits`) so that it can handle the rounding properly, rather than to truncate the output it generates with a wrong digit being already rounded and then try to retrofit the correct rounding afterwards.  
  
4. There is a shortcut branch for the case when there are at most 2 digits to print (see [this line]()https://github.com/boostorg/charconv/blob/0dac3d5dd4da8ee1760d9363aa5986178d8f582f/include/boost/charconv/detail/dragonbox/floff.hpp#L1482). I haven't thought about it carefully, but I guess you may need to adjust that part as well.  
  
  
By the way, this doesn't really matter but there is something that could confuse future contributors (may include myself years later being clueless about what I wrote):  
  
https://github.com/boostorg/charconv/blob/0dac3d5dd4da8ee1760d9363aa5986178d8f582f/include/boost/charconv/detail/dragonbox/floff.hpp#L1475  
  
In the line above, `has_more_segments` is supposed to be `true` if the result of multiplication is *not* an integer. The code as is is correct, but it is just that the variable naming is semantically opposite. I compared it with the original code and now I understand why it went like that. I mean, I was just lazy and wanted to reuse `compute_mul_result` but at the same time wanted to have `has_more_segments` rather than `no_more_segments` so just inverted the field.  
  
So I suggest to change L1471 with `compute_mul_result{r.high, r.low == 0}` and change L1475 with `has_more_segments = !segments.is_integer`. I may need to correct the original code as well.

---

## Comment 7

> Username: mborland  
> Created at: 2024-02-14 07:59:46 UTC  
> Url: https://github.com/boostorg/charconv/issues/158#issuecomment-1943247593  

> Hi Matt,  
>   
> I tested with `double d = 1e-17` and there seems to be still some issues.  
>   
> 1. For `chars_format::scientific`,  
>   
> It seems now it prints one less digits than needed. The way `chars_format::scientific` and `chars_format::general` interpret the precision argument are different, since the former counts the number of digits after the decimal dot, while the latter counts the number of all printed decimal mantissa digits, i.e., the latter should print one less digit than the former when the same `precision` argument is given (because it includes the first digit when counting the precision, while the former doesn't).  
>   
  
What value and precision are you using? I added https://github.com/boostorg/charconv/pull/161/files#diff-070074e8df492c3e6388dbfe33be482a030aadde518cc3c2cf11bf6772f707aeR231-R232 which tests every precision from -1 (unspecified) to digits10 and then 40-50, and the results match against GCC and MSVC. I also have your original values here: https://github.com/boostorg/charconv/pull/161/files#diff-18b6498578f2df280f549e9146e4203e12f542897554dfb1d88407f738687290R28-R40  
  
> 2. For `chars_format::general`,  
>   
> The way `fixed` vs `scientifc` is chosen seems wrong to me. As far as I know the rule follows that of `%g` of `std::printf`. According to https://en.cppreference.com/w/cpp/io/c/fprintf, `fixed` is chosen if (1) for the number of digits to be printed is strictly larger than the decimal exponent, and (2) the decimal exponent is at least `-4`, which basically means that:  
>   
> * When the exponent is nonnegative, every digit of the integer part is to be printed, and  
> * When the exponent is negative, the face value of the printed string must be at least `0.0001`.  
>   
> And `scientific` is chosen otherwise. In particular, anything smaller than `0.0001` should be printed in `scientific` format, but the way Boost.Charconv currently works doesn't seem to agree with that.  
>   
  
I believe you are correct. Fixed in this commit: https://github.com/boostorg/charconv/pull/161/commits/6ff47d1dbe47f847c283adeafaf2e8bafbdf108f  
  
> 3. For `chars_format::fixed`,  
>   
> The number of digits printed seems correct, but the rounding logic seems wrong. I believe the relevant line is here:  
>   
> https://github.com/boostorg/charconv/blob/0dac3d5dd4da8ee1760d9363aa5986178d8f582f/include/boost/charconv/detail/dragonbox/floff.hpp#L3844  
>   
> The round-up should be done according to "round-to-nearest, tie-to-even" rule, which means:  
>   
> * Either the digit-at-rounding is strictly larger than 5, or  
> * Is equal to 5 and:  
>     
>   * Either there are further nonzero digits after the digit-at-rounding, or  
>   * there is no further nonzero digit but the last digit before the digit-at-rounding is odd.  
>   
> Also, if rounded-up digit were `9`, then the addition needs to propagate back to earlier digits as well. At the worst case this may even change the exponent, e.g. like when `99....99` is rounded to `100....00`. The proper handling of all these is really annoying, and I think maybe the easiest way to do this correctly is to let `floff` to do it. That is, I think you need to adjust upfront the precision parameter that `floff` uses (either `precision` or `remaining_digits`) so that it can handle the rounding properly, rather than to truncate the output it generates with a wrong digit being already rounded and then try to retrofit the correct rounding afterwards.  
>   
  
Yeah I ran into a similar issue with adapting Ryu for 80 and 128-bit types since they aren't provided out of the box. I guess with a comparison at the top it should be easier to shave precision knowing the result with have a negative decimal exponent.  
  
> 4. There is a shortcut branch for the case when there are at most 2 digits to print (see this linehttps://github.com/boostorg/charconv/blob/0dac3d5dd4da8ee1760d9363aa5986178d8f582f/include/boost/charconv/detail/dragonbox/floff.hpp#L1482  
>    ). I haven't thought about it carefully, but I guess you may need to adjust that part as well.  
>   
> By the way, this doesn't really matter but there is something that could confuse future contributors (may include myself years later being clueless about what I wrote):  
>   
> https://github.com/boostorg/charconv/blob/0dac3d5dd4da8ee1760d9363aa5986178d8f582f/include/boost/charconv/detail/dragonbox/floff.hpp#L1475  
>   
> In the line above, `has_more_segments` is supposed to be `true` if the result of multiplication is _not_ an integer. The code as is is correct, but it is just that the variable naming is semantically opposite. I compared it with the original code and now I understand why it went like that. I mean, I was just lazy and wanted to reuse `compute_mul_result` but at the same time wanted to have `has_more_segments` rather than `no_more_segments` so just inverted the field.  
>   
> So I suggest to change L1471 with `compute_mul_result{r.high, r.low == 0}` and change L1475 with `has_more_segments = !segments.is_integer`. I may need to correct the original code as well.  
  
I'll make the change.

---

## Comment 8

> Username: jk-jeon  
> Created at: 2024-02-14 08:42:13 UTC  
> Url: https://github.com/boostorg/charconv/issues/158#issuecomment-1943303588  

>I also have your original values here: https://github.com/boostorg/charconv/pull/161/files#diff-18b6498578f2df280f549e9146e4203e12f542897554dfb1d88407f738687290R28-R40  
  
In line 34 of this file (test/github_issue_158.cpp), `1.0000000000000000715424240546219245085280561849232e-17` has 49 digits after the decimal dot and I think it should print one more digit since the precision is 50.  
  
(To be more precise, I'm not quite sure what you are supposed to do with possible trailing zeros... do they need to be printed, or removed, or either is fine... I don't know. But in this specific case the next digit is not zero anyway.)  
  
It seems to me that MS STL produces one more digit (`5`).  
  
>What value and precision are you using? I added https://github.com/boostorg/charconv/pull/161/files#diff-070074e8df492c3e6388dbfe33be482a030aadde518cc3c2cf11bf6772f707aeR231-R232 which tests every precision from -1 (unspecified) to digits10 and then 40-50, and the results match against GCC and MSVC.  
  
I think `boost::charconv::to_chars` currently produces the correct number of digits for nonnegative exponents but one less for negative exponents.  
  
>I believe you are correct. Fixed in this commit: https://github.com/boostorg/charconv/commit/6ff47d1dbe47f847c283adeafaf2e8bafbdf108f  
  
I still have some doubt but let me think about it tomorrow... it's too late here to be productive right now.  
  
Thanks!

---

## Comment 9

> Username: mborland  
> Created at: 2024-02-14 10:26:44 UTC  
> Url: https://github.com/boostorg/charconv/issues/158#issuecomment-1943473057  

With https://github.com/boostorg/charconv/pull/161/commits/4e6e2b55e8ad6e9c76973c456d98c2b109242af2 the number of digits produced matches GCC and MSVC. I updated the test sets to make sure.

---

## Comment 10

> Username: mborland  
> Created at: 2024-02-14 11:04:08 UTC  
> Url: https://github.com/boostorg/charconv/issues/158#issuecomment-1943538757  

I believe https://github.com/boostorg/charconv/pull/161/commits/e7769e381b8d1ea79fc0cc6fa1b66005440a4cb7 is on the right path to getting floff to give us the correct rounding instead of post-processing. Works correctly for the test case of 1e-17 resulting in `0.00000000000000001000000000000000071542424054621925` which was the one that required the post-processing rounding.

---

## Comment 11

> Username: mborland  
> Created at: 2024-02-15 11:18:36 UTC  
> Url: https://github.com/boostorg/charconv/issues/158#issuecomment-1945888293  

In https://github.com/boostorg/charconv/pull/164 the only failing test case is now 1e-21 to 50 digits of precision which should be `0.00000000000000000000099999999999999990753745222790` but is returning the incorrectly rounded `...89`

---

## Comment 12

> Username: mborland  
> Created at: 2024-02-16 08:44:01 UTC  
> Url: https://github.com/boostorg/charconv/issues/158#issuecomment-1947972136  

I believe everything related to floff handling has been resolved. Similar fixes need to be applied to Ryu for 80 and 128 bit type handling

---

## Comment 13

> Username: jk-jeon  
> Created at: 2024-02-20 02:05:05 UTC  
> Updated at: 2024-02-20 06:28:09 UTC  
> Url: https://github.com/boostorg/charconv/issues/158#issuecomment-1953372441  

Dear Matt,  
  
I think there still are several issues. I tried to edit the code quite a lot to fix them. I will make a PR soon, but before that I want to make sure for which inputs the current code generates wrong outputs, which requires some more investigation from my side.  
  
Also, I found some more issues that are not quite related to this thread, so I will open a new issue about them.  
  
EDIT:  
I committed the change into my [fork](https://github.com/jk-jeon/charconv) so meanwhile you can test it out.
