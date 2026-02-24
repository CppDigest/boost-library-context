# #297 fix uint_parser<T(signed)> overflow problem [Merged]

> Username: wanghan02  
> Created at: 2017-11-27 16:55:08 UTC  
> Updated at: 2018-11-12 20:24:08 UTC  
> Merged at: 2017-12-01 10:43:44 UTC  
> Closed at: 2017-12-01 10:43:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/297  

According to spirit document,  
  
  "All numeric parsers check for overflow conditions based on the type T the corresponding uint_parser<> has been instantiated with. If the parsed number overflows this type the parsing fails. Please be aware that the overflow check is not based on the type of the supplied attribute but solely depends on the template parameter T."  
  
the valid input range of `x3::uint_parser<T>` should be `0` - `std::numeric_limits<T>::max()`. However, the current implementation takes values from `std::numeric_limits<T>::max()+1` - `std::numeric_limits<std::make_unsigned_t<T>>::max()` also as valid input when `T` is a signed integral.  
  
Example:  
  `x3::uint_parser<signed char>` should parse 0-127, but with current implementation 128-255 also works.  
  
Test cases are added.

---

## Comment 1

> Username: djowel  
> Created_at: 2017-12-01 00:42:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/297#issuecomment-348367286  

Is this for X3 or Qi? Or it seems both?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2017-12-01 00:43:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/297#issuecomment-348367426  

Now for both.

---

## Comment 3

> Username: djowel  
> Created_at: 2017-12-01 00:43:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/297#issuecomment-348367526  

Great!

---

## Comment 4

> Username: djowel  
> Created_at: 2017-12-01 00:44:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/297#issuecomment-348367716  

looks good to me.

---

## Comment 5

> Username: wanghan02  
> Created_at: 2017-12-01 07:07:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/297#issuecomment-348418642  

@Kojoley Thanks!

---

## Comment 6

> Username: Kojoley  
> Created_at: 2017-12-01 10:44:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/297#issuecomment-348462944  

Thanks for the patch @wanghan02!

---

## Comment 7

> Username: mwpowellhtx  
> Created_at: 2018-11-12 16:38:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/297#issuecomment-437947575  

@djowel I'm not positive I've just bumped into this, or a similar, [kerfuffle](http://coliru.stacked-crooked.com/a/0c0f106c9952e2e1) as well in Qi. Base 10 parses negative values just fine, but has difficulty dealing with negative base 16 (hex) and base 8 (octal) integers. See [example code](http://coliru.stacked-crooked.com/a/0c0f106c9952e2e1) for illustration.

---

## Comment 8

> Username: Kojoley  
> Created_at: 2018-11-12 18:02:52 UTC  
> Updated_at: 2018-11-12 19:52:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/297#issuecomment-437975231  

> I'm not positive I've just bumped into this, or a similar  
  
The problem in your example has nothing with the PR (it was about `uint_parser` while you are using `int_parser`), you can confirm that by testing on previous versions. What you are trying to achieve was never supported (int parsers explicitly expects minus sign in front of a number to threat it as a negative https://github.com/boostorg/spirit/blob/35c6a9a1bbf10004598f963604b8b881b347920d/include/boost/spirit/home/qi/numeric/numeric_utils.hpp#L120-L124). It is also a platform dependent thing, but should not be that hard for you to implement yourself.  
  
Here is how I would done it (for two's complement; if I was needed to):  
```cpp  
template <typename T>  
bool parse_int_hex(..., T& out)  
{  
    make_unsigned_t<T> value;  
    if (!parse(..., value)) return false;  
    constexpr auto max_positive = static_cast<make_unsigned_t<T>>(std::numeric_limits<T>::max());  
    if (value > max_positive)  
        out = static_cast<T>(value - max_positive) + std::numeric_limits<T>::min() - 1;  
    else  
        out = static_cast<T>(value);  
    return true;  
}  
```  
https://wandbox.org/permlink/58jpePpKXgOPvCc3

---

## Comment 9

> Username: mwpowellhtx  
> Created_at: 2018-11-12 19:03:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/297#issuecomment-437993594  

@Kojoley Good to know, thank you for clarifying that. Apologies for the detour.

---

## Comment 10

> Username: mwpowellhtx  
> Created_at: 2018-11-12 20:24:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/297#issuecomment-438016743  

@Kojoley So, just to clarify, while I can ``boost::format`` negative values to hex, for instance, just fine, the parsing side of the equation does not support that, I would need to produce a kind of signed-int attribute involving any signage that were part of the input stream. i.e. in C++ pseudo code:  
  
```  
enum num_sign_t : char { sign_minus = '-', sign_plus = '+' };  
struct int_t {  
    boost::optional<num_sign_t> sign;  
    std::intmax_t _value;  
    std::intmax_t get_value() const;  
}  
```  
  
It's a bit of work, but it is more consistent with the language specification I am dealing with at the moment. Would also need to be cautious of corner cases like "negative max-int", whose edge case could really only support no more than "negative (max-int + 1)", I think. Along these lines. Could be a corner case unless I can specify the maximum value of the "positive" side.  
  
Okay, well, enough of this tangent... Thanks again!

---
