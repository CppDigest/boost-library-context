# #66 - Test failures of `to_static_string` with floating point types when using libstdc++-15 / C++26 [Closed]

> Username: Flamefire  
> Created at: 2025-10-01 11:53:31 UTC  
> Updated at: 2025-10-23 15:39:12 UTC  
> Closed at: 2025-10-23 15:39:12 UTC  
> Url: https://github.com/boostorg/static_string/issues/66  

On Ubuntu 25.04 the `to_static_string` tests fail for floating point types, likely because the underlying libstdc++ is that of GCC 15, as I see the same failures for the used Clang versions.  
  
See https://github.com/boostorg/static_string/actions/runs/18156408010/job/51686048189  
  
>  libs/static_string/test/static_string.cpp(3955): test 'str == std_res' ('0.100000' == '0.1') failed in function 'void boost::static_strings::testToStaticString()'  
libs/static_string/test/static_string.cpp(3956): test 'str == std_res' ('0.000000' == '1e-07') failed in function 'void boost::static_strings::testToStaticString()'  
libs/static_string/test/static_string.cpp(3957): test 'str == std_res' ('-0.000000' == '-1e-07') failed in function 'void boost::static_strings::testToStaticString()'  
libs/static_string/test/static_string.cpp(3958): test 'str == std_res' ('-0.100000' == '-0.1') failed in function 'void boost::static_strings::testToStaticString()'  
libs/static_string/test/static_string.cpp(3959): test 'str == std_res' ('1234567890.000100' == '1234567890.0001') failed in function 'void boost::static_strings::testToStaticString()'  
libs/static_string/test/static_string.cpp(3960): test 'str == std_res' ('1.123457' == '1.123456789012345') failed in function 'void boost::static_strings::testToStaticString()'  
libs/static_string/test/static_string.cpp(3961): test 'str == std_res' ('-1234567890.123400' == '-1234567890.1234') failed in function 'void boost::static_strings::testToStaticString()'  
libs/static_string/test/static_string.cpp(3962): test 'str == std_res' ('-1.123457' == '-1.123456789012345') failed in function 'void boost::static_strings::testToStaticString()'  
libs/static_string/test/static_string.cpp(3968): test 'str == std_res' ('0.000000' == '3.3621031431120935063e-4932') failed in function 'void boost::static_strings::testToStaticString()'  
libs/static_string/test/static_string.cpp(3969): test 'str == std_res' ('0.000000' == '1.1754944e-38') failed in function 'void boost::static_strings::testToStaticString()'  
  
Note that I changed the tests to use `BOOST_TEST_EQ` to see the actual values on failure.  
  
`'-1.123457' == '-1.123456789012345'` seems most worrying as the rounding is wrong: The value is cut off

---

## Comment 1

> Username: gennaroprota  
> Created at: 2025-10-02 09:42:06 UTC  
> Url: https://github.com/boostorg/static_string/issues/66#issuecomment-3360181073  

@vinniefalco: Is it a requirement that the result be the same as `std::to_string()`?

---

## Comment 2

> Username: Flamefire  
> Created at: 2025-10-02 11:53:10 UTC  
> Url: https://github.com/boostorg/static_string/issues/66#issuecomment-3360815597  

Shouldn't it be at least exact according to rounding rules? `-1.123456789012345` roundtrips with the stdlib but causes a truncation with static_string. I'd expect it at least to round to `-1.123458'  
  
I can't find documentation of the behavior of `to_static_string`. Given the resemblance to `std::to_string` I'd intuitively expect the same behavior

---

## Comment 3

> Username: vinniefalco  
> Created at: 2025-10-02 16:42:04 UTC  
> Url: https://github.com/boostorg/static_string/issues/66#issuecomment-3362124302  

`to_static_string` should produce results identical as `std::to_string` although if there is a problem with that design choice then it needs discussion (maybe on the list but we aren't there yet)

---

## Comment 4

> Username: gennaroprota  
> Created at: 2025-10-03 09:02:32 UTC  
> Updated at: 2025-10-03 09:10:50 UTC  
> Url: https://github.com/boostorg/static_string/issues/66#issuecomment-3364872247  

@Flamefire: I don't see how the standard result can be something like "0.1", given that `std::to_string( double )` must behave as if it used `std::sprintf()` with a "%f" specifier, and that has a default precision of 6. In fact, [this](https://godbolt.org/z/n4c3vTToj) gives "0.100000".

---

## Comment 5

> Username: Flamefire  
> Created at: 2025-10-03 09:56:08 UTC  
> Url: https://github.com/boostorg/static_string/issues/66#issuecomment-3365095588  

Good question, seems for c++2c this changes: https://godbolt.org/z/7K84j5sEW  
  
Double checking https://en.cppreference.com/w/cpp/string/basic_string/to_string for that I found  
> Converts a numeric value to a string as if by `std::format("{}", value)`.	(since C++26)  
  
And  
> __cpp_lib_to_string 	202306L 	(C++26) 	Redefining std::to_string in terms of std::format  
  
Following that leads to https://en.cppreference.com/w/cpp/utility/format/spec.html  
> Decimal format. Produces the output as if by calling [std::to_chars](https://en.cppreference.com/w/cpp/utility/to_chars.html)(first, last, value).  
  
https://en.cppreference.com/w/cpp/utility/to_chars.html  
> value is converted [...] in the default ("C") locale. [...] shortest representation: the string representation consists of the smallest number of characters such that there is at least one digit before the radix point (if present) and parsing the representation using the corresponding `std::from_chars` function recovers value exactly. If there are several such representations, one with the smallest difference to value is chosen, resolving any remaining ties using rounding according to `std::round_to_nearest`  
  
So `0.1` is correct for C++26  
  
Not sure how hard this would be, you could of course just adjust the tests to check against  `snprintf("%f"` and state that clearly in the documentation.  
  
But `0.000000` for `to_static_string(1e-7)` seems odd as `1e-7` is possible. Not sure how hard that would be to implement.     
Maybe in C++26 `std::to_chars` could simply be used? Looks trivial to do and likely faster

---

## Comment 6

> Username: vinniefalco  
> Created at: 2025-10-03 22:18:34 UTC  
> Url: https://github.com/boostorg/static_string/issues/66#issuecomment-3367383072  

C++26 introduced a change that causes different output for the same code?

---

## Comment 7

> Username: Flamefire  
> Created at: 2025-10-04 08:51:25 UTC  
> Url: https://github.com/boostorg/static_string/issues/66#issuecomment-3368035075  

> C++26 introduced a change that causes different output for the same code?  
  
Yes, it redefined `std::to_string` to be more consistent with the "new" `std::format` to output the shortest sequence.     
I don't see a big issue of outputting `0.1` instead of `0.100000000000` in general although of course it is possible that some might be affected.

---

## Comment 8

> Username: gennaroprota  
> Created at: 2025-10-06 10:33:41 UTC  
> Url: https://github.com/boostorg/static_string/issues/66#issuecomment-3370954016  

The documentation of the `to_static_[w]string()` overloads just says "Converts value to a static_[w]string", so we have the option to either make it return the same result as `to_[w]string()`, which means we need two different implementations, one for pre-C++26 and one for C++26 or later, or stick with one implementation and possibly document how we do the conversion. @vinniefalco: Which option do you prefer?

---

## Comment 9

> Username: Flamefire  
> Created at: 2025-10-06 11:48:30 UTC  
> Url: https://github.com/boostorg/static_string/issues/66#issuecomment-3371240205  

C++17 has `std::to_chars` and C++11 Boost.Charconv can be used prior to that as a drop-in. Maybe just always use (either of)  that?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2025-10-06 18:25:10 UTC  
> Url: https://github.com/boostorg/static_string/issues/66#issuecomment-3373250550  

I don't know what the best solution is, and I think we should strive to satisfy users first and foremost. What do they want?

---

## Comment 11

> Username: gennaroprota  
> Created at: 2025-10-23 15:39:12 UTC  
> Url: https://github.com/boostorg/static_string/issues/66#issuecomment-3437708513  

Fixed with 2cc22bf5a80196965a0b62e76c802ace2ee953ca.
