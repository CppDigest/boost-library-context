# #169 Try to fix #158 [Merged]

> Username: jk-jeon  
> Created at: 2024-02-21 03:03:16 UTC  
> Updated at: 2024-02-21 12:38:30 UTC  
> Merged at: 2024-02-21 12:38:29 UTC  
> Closed at: 2024-02-21 12:38:29 UTC  
> Url: https://github.com/boostorg/charconv/pull/169  

- Remove https://github.com/boostorg/charconv/blob/f5e197177b433e3fa35627ce862fab799cfa4812/src/to_chars_float_impl.hpp#L648~661.  
  
Suppose `fmt == chars_format::scientific`. In this case, precision is decreased by `static_cast<int>(abs_value < 1)` (https://github.com/boostorg/charconv/blob/f5e197177b433e3fa35627ce862fab799cfa4812/src/to_chars_float_impl.hpp#L651) and then remaining_digits is set by this value plus `static_cast<int>(abs_value < 1) + 1` (https://github.com/boostorg/charconv/blob/f5e197177b433e3fa35627ce862fab799cfa4812/include/boost/charconv/detail/dragonbox/floff.hpp#L1454). This is same as just `precision + 1`, except that if `precision = 0` and `abs_value < 1`, precision passed into floff is `0` rather than `-1`, resulting in `remaining_digits = 2`. This causes unnecessary one more digits to be printed. (ex: `1e-1` with `chars_format::scientific` and `precision = 0`).  
  
For other formats, I think it is better to do all the precision adjustment inside floff. The current implementation is wrong, for example, if precision is too small, because there is a separate branch for the case `remaining_digits <= 2` (https://github.com/boostorg/charconv/blob/f5e197177b433e3fa35627ce862fab799cfa4812/include/boost/charconv/detail/dragonbox/floff.hpp#L1490) before the precision ever has a chance to be corrected for the fixed format case. (ex: `1e-1` with `fmt = chars_format::fixed` and `precision = 0`).  
  
  
- Temporary buffer for `chars_format::general`.  
  
I guess this is not ideal, but I found no better way than to just have a temporary buffer for the case of `chars_format::general`. In this route, floff first writes the output into a temporary stack-allocated buffer, and then this is copied back into the user-provided buffer. Generally, checking for possible buffer overflow for `chars_format::general` causes a lot of headaches. First, since `chars_format::general` needs to trim all trailing zeros, we cannot reliably know the needed buffer size before we complete doing the rounding. Also, since rounding can cause the exponent to be increased, we cannot really choose fixed vs scientific before complete doing the rounding. And we can do rounding only after generating all the digits. In theory, I think it should be possible to do all of these without actually writing to the buffer, but that requires a lot of changes to the floff implementation which I want to avoid.  
  
I also modified the handling of `chars_format::general` inside floff. To correctly address all of the mentioned issues, I first wrote all the digits into the temporary buffer (with one margin at the starting position of the buffer for the decimal dot), do the rounding, and then decide fixed vs scientific, move the printed digits around and insert decimal dot and remove trailing zeros, and then copy it back to the user-provided buffer with overflow check.  
  
The current code (before the PR) handles `chars_format::general` not quite correctly. For example, for `0.1` with `precision = 2`, it tries to write `0.100` while it thinks buffer size being 2 is enough (hence commits buffer overrun). Even with large enough buffer, it fails to trim trailing zeros.  
  
  
- On https://github.com/boostorg/charconv/blob/f5e197177b433e3fa35627ce862fab799cfa4812/include/boost/charconv/detail/dragonbox/floff.hpp#L1329 and on https://github.com/boostorg/charconv/blob/f5e197177b433e3fa35627ce862fab799cfa4812/include/boost/charconv/detail/dragonbox/floff.hpp#L1397, the exact code is duplicated, so we can pull it out of the branch.  
  
  
- Currently `0` is always formatted as `0.00...0e+00`. But my understanding of the spec is that, we print `0.00...0e+00` for `chars_format::scientific` (with `precision`-many `0`'s after `.`), `0.00...0` for `chars_format::fixed` (with `precision`-many `0`'s after `.`), and just `0` for `chars_format::general`. So I edited https://github.com/boostorg/charconv/blob/f5e197177b433e3fa35627ce862fab799cfa4812/include/boost/charconv/detail/dragonbox/floff.hpp#L1416~1440 accordingly.  
  
  
- Remove https://github.com/boostorg/charconv/blob/f5e197177b433e3fa35627ce862fab799cfa4812/include/boost/charconv/detail/dragonbox/floff.hpp#L1452~1464.  
  
Precision adjustment and buffer overflow check is done right after computing the first segment in Phase 1 (before the `remaining_digits <= 2` branch appears).  
  
  
- Add precision adjustment and buffer overflow check.  
  
Add some code to figure out the correct number of digits to be printed. This also does buffer overflow check and adjusts the decimal exponent (thus adjustments on decimal_exponent made in Phase 1 were removed). For `chars_format::fixed` with positive exponent, we cannot reliably compute the needed buffer length at this point because things like 99999.99XX... can be rounded to 100000.0 in which case we need one more character to print than usual. This possibility is inspected when we actually do rounding. For `chars_format::general`, also because of rounding, it is impossible to relibaly decide between fixed vs scientific at this point.  
  
  
- Simplify the `remaining_digits <= 2` branch.  
  
Since the digit length of the first segment needs to be computed upfront, in the branch `remaining_digits <= 2` we already know it, and also have the first segment aligned to have 19 digits by adding trailing zeros. Thanks to this, the handling of this branch can be a lot simpler. Also, what needs to be actually printed can be quite different depending on scientific vs fixed formats, so I just reused the existing rounding lables rather than do some specialized stuffs in this branch.  
  
  
- Since the precision adjustment is done upfront, we don't need the branch on [floff.hpp](https://github.com/boostorg/charconv/blob/f5e197177b433e3fa35627ce862fab799cfa4812/include/boost/charconv/detail/dragonbox/floff.hpp)#L1787.  
  
  
- I changed what happens inside the `round_up_all_9s` label, because `chars_format::fixed` requires some different handling.  
  
  
- In github_issue_158.cpp, I think `last` argument should be `buffer + sizeof(buffer) - 1` because we will add null terminator.  
  
- Add a bit more test cases.

---

## Comment 1

> Username: mborland  
> Created_at: 2024-02-21 07:09:25 UTC  
> Url: https://github.com/boostorg/charconv/pull/169#issuecomment-1956020835  

I merged develop into your branch to fix the merge conflicts. I resolved all conflicts by keeping what was on your branch. Hopefully it didn't mess anything up.

---

## Comment 2

> Username: jk-jeon  
> Created_at: 2024-02-21 08:03:58 UTC  
> Url: https://github.com/boostorg/charconv/pull/169#issuecomment-1956087438  

Thanks, I was to resolve the conflict but had to do something else for a while.  
  
Your CI/tests are darn great. I'll fix any bugs revealed!

---

## Comment 3

> Username: jk-jeon  
> Created_at: 2024-02-21 09:09:21 UTC  
> Url: https://github.com/boostorg/charconv/pull/169#issuecomment-1956194648  

Hmm... not sure what happend to continuous-integration/drone/pr...

---

## Comment 4

> Username: mborland  
> Created_at: 2024-02-21 09:14:27 UTC  
> Url: https://github.com/boostorg/charconv/pull/169#issuecomment-1956203664  

> Hmm... not sure what happend to continuous-integration/drone/pr...  
  
I manually restarted it. The s390x runners are the most finicky, and sometimes they fail in cloning.

---

## Comment 5

> Username: mborland  
> Created_at: 2024-02-21 10:58:02 UTC  
> Url: https://github.com/boostorg/charconv/pull/169#issuecomment-1956396334  

Since codecov doesn't like the manual restarts here is the coverage report now that CI is green: https://app.codecov.io/gh/boostorg/charconv/pull/169. Looks pretty good.

---

## Comment 6

> Username: jk-jeon  
> Created_at: 2024-02-21 11:04:02 UTC  
> Updated_at: 2024-02-21 11:04:17 UTC  
> Url: https://github.com/boostorg/charconv/pull/169#issuecomment-1956406536  

Great, if I have some more time I will add some more test cases (as a separate PR) that cover the missing lines. The ones introduced by this PR should be easy to cover. The ones which I guess were already there may require some heavy investigation though.

---

## Comment 7

> Username: mborland  
> Created_at: 2024-02-21 12:38:25 UTC  
> Url: https://github.com/boostorg/charconv/pull/169#issuecomment-1956561406  

> Great, if I have some more time I will add some more test cases (as a separate PR) that cover the missing lines. The ones introduced by this PR should be easy to cover. The ones which I guess were already there may require some heavy investigation though.  
  
Sounds good. Thanks for this!

---
