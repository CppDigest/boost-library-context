# #520 - conversion warning [Closed]

> Username: vinniefalco  
> Created at: 2021-03-04 21:48:42 UTC  
> Updated at: 2021-06-23 05:52:35 UTC  
> Closed at: 2021-06-23 05:52:35 UTC  
> Url: https://github.com/boostorg/json/issues/520  

This line gives a conversion warning:  
  
https://github.com/boostorg/json/blame/develop/include/boost/json/basic_parser_impl.hpp#L147  
  
If the code is changed thusly, the assert is triggered by tests:  
```  
    double x = static_cast<double>(m);  
    BOOST_ASSERT(static_cast<std::uint64_t>(x) == m);  
```  
  
The warning is:  
  
> warning C4244: 'initializing': conversion from 'uint64_t' to 'double', possible loss of data

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-03-12 15:46:49 UTC  
> Url: https://github.com/boostorg/json/issues/520#issuecomment-797574117  

The triggered assert is a natural result of `m` being too big to be stored in a `double`. There's no way to avoid this imprecision. This is also related to Bishop Fox security assessment, because it opens up a possibility of 2 libraries parsing the same number differently. There are several things I could do:  
  
 1.  Ignore the imprecision and revert back to using `static_cast` (I was not aware this causes a warning, it doesn't do it with clang or gcc even with `pedantic` warnings).  
 2. Refuse all inputs that cause imprecision and throw an exception or error; document accepted number ranges.  
 3. Allow users to choose the desired behaviour (we still have to choose a default, though).

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-03-12 22:46:56 UTC  
> Url: https://github.com/boostorg/json/issues/520#issuecomment-797798683  

@pdimov is this correct?

---

## Comment 3

> Username: pdimov  
> Created at: 2021-03-12 22:51:24 UTC  
> Url: https://github.com/boostorg/json/issues/520#issuecomment-797800222  

Is what correct? Yes, not all int64 values can be represented in a double without loss of precision. double only has 53 bits.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-03-12 23:41:17 UTC  
> Url: https://github.com/boostorg/json/issues/520#issuecomment-797815716  

The parser already takes into account the loss of precision before this function is called. I am asking, if Dmitry is correct that those are the only three options. I think it needs to not warn under any circumstance. 2 and 3 in the list above don't sound right at all.

---

## Comment 5

> Username: pdimov  
> Created at: 2021-03-12 23:43:54 UTC  
> Url: https://github.com/boostorg/json/issues/520#issuecomment-797816475  

I honestly don't know what these "options" are about. There's nothing wrong with the code.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2021-03-13 00:29:11 UTC  
> Url: https://github.com/boostorg/json/issues/520#issuecomment-797830497  

MSVC gives a warning. If we can't change it to not warn then we have to ifdef to turn the warning off.

---

## Comment 7

> Username: pdimov  
> Created at: 2021-03-13 00:30:05 UTC  
> Url: https://github.com/boostorg/json/issues/520#issuecomment-797830683  

What's wrong with the static_cast?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2021-03-13 00:33:09 UTC  
> Url: https://github.com/boostorg/json/issues/520#issuecomment-797831358  

```  
2>boost/json/basic_parser_impl.hpp(147,14):  
warning C4244: 'initializing': conversion from 'uint64_t' to 'double', possible loss of data  
```

---

## Comment 9

> Username: vinniefalco  
> Created at: 2021-03-13 00:34:11 UTC  
> Url: https://github.com/boostorg/json/issues/520#issuecomment-797831661  

If I change the code to use a static cast followed by assert, then the assert is triggered:  
```  
    double x = static_cast<double>(m);  
    BOOST_ASSERT(static_cast<std::uint64_t>(x) == m);  
```  
  
I guess the assert is not valid, because there are legitimate cases where we perform a lossy conversion intentionally?

---

## Comment 10

> Username: pdimov  
> Created at: 2021-03-13 00:54:04 UTC  
> Url: https://github.com/boostorg/json/issues/520#issuecomment-797835750  

All conversions to floating point are lossy, with some small exceptions. Since double can't represent more than 53 bits of mantissa, if you have more than that, there will obviously be loss of precision.  
  
The goal when converting a decimal number to double is not to not have loss of precision - which is impossible except in rare cases - it's to obtain a result that is the closest representable double to the input (or not more than K least significant bits away from the closest representable).  
  
Since the function returns double, if `m` doesn't fit in a double, there's not much you can do about it. Consider the trivial case where `e` is 0. What are you suggesting the function should return if `m` cannot be represented exactly?

---

## Comment 11

> Username: vinniefalco  
> Created at: 2021-03-13 00:58:25 UTC  
> Url: https://github.com/boostorg/json/issues/520#issuecomment-797836689  

> Consider the trivial case where e is 0.  
  
Yeah, well my point is that this function is not called in that case. So I think the proper fix is to just use `static_cast`, and don't try to assert that the conversion was exact. @grisumbras

---

## Comment 12

> Username: grisumbras  
> Created at: 2021-06-23 05:52:35 UTC  
> Url: https://github.com/boostorg/json/issues/520#issuecomment-866549323  

This seems to have been resolved (as the static assert is not there any more).
