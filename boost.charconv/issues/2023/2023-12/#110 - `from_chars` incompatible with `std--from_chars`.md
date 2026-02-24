# #110 - `from_chars` incompatible with `std::from_chars` [Closed]

> Username: akrzemi1  
> Created at: 2023-12-26 19:59:05 UTC  
> Updated at: 2024-01-18 06:58:15 UTC  
> Closed at: 2024-01-18 06:58:15 UTC  
> Url: https://github.com/boostorg/charconv/issues/110  

The following behavior is described in [from_chars for floating point types](https://develop.charconv.cpp.al/#from_chars_from_chars):  
  
> On `std::errc::result_out_of_range` we return `±0` for small values (e.g. 1.0e-99999) or `±HUGE_VAL` for large values (e.g. 1.0e+99999) to match the handling of `std::strtod`. This is a divergence from the standard which states we should return the `value` argument unmodified.  
  
This divergence from `std::from_chars` is unacceptable in a library that claims to be a faster replacement for the STD component. `std::from_chars` gives a practical guarantee: either we populated the new value as per your request, or we didn't touch your value. One can easily imagine how this strong (commit-or-rollback) error guarantee is useful. And it will silently disappear when someone replaces `std::from_chars` with `boost::from_chars` for efficiency.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-12-26 20:05:43 UTC  
> Url: https://github.com/boostorg/charconv/issues/110#issuecomment-1869748474  

ERANGE doesn't give you enough information how exactly the result was out of range (there are four possible ways), and the current behavior is more useful in practice because it's what e.g. Boost.JSON needs.

---

## Comment 2

> Username: pdimov  
> Created at: 2023-12-26 20:07:52 UTC  
> Url: https://github.com/boostorg/charconv/issues/110#issuecomment-1869749489  

See https://cplusplus.github.io/LWG/lwg-active.html#3081.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2023-12-27 10:10:27 UTC  
> Url: https://github.com/boostorg/charconv/issues/110#issuecomment-1870152608  

Thanks for the link.  
So the current design, for huge values, tries to implement -- rather than `std::from_chars` -- a proposed (but not accepted) modification in issue [LWG 3081](https://cplusplus.github.io/LWG/lwg-active.html#3081).  
  
There are two problems with it.   
1. Until this solution is adopted, which may be years, `boost::from_chars` will be incompatible with `std::from_chars`.  
2. If WG21 ultimately chooses a different solution, you will bake the incompatibility with `std::from_chars` forever.  
  
Now, I understand that the JSON parser use case is that you want to treat super-small numbers as zero and super-huge values as Inf. I acknowledge that, but I would not spoil the clear interface of `from_chars` for that reason. By "clear" I mean:  
  
1. Either there was an error or not: you can tell that from `ec`.  
2. If there was an error, the return value tells you what it was, and `value` remains unchanged.  
  
A solution acceptable by me would be to either expand `from_chars_result` to convey more information, or to allow the programmer to specify their expectations (such as the JSON parser use case) directly by passing dedicated function parameters.  
  
Of course that would make the interface of `boost::from_chars` depart from `std::from_chars`. Therefore, until [LWG 3081](https://cplusplus.github.io/LWG/lwg-active.html#3081) is resolved, I would propose to do two things:  
1. Keep the contract of  `boost::from_chars` compliant with `std::from_chars`.  
2. Offer another function, like `from_chars_full_float_range`, that still has the "clear interface" as defined above and that returns values/errors as required by the JSON parser use case.

---

## Comment 4

> Username: pdimov  
> Created at: 2023-12-27 12:36:12 UTC  
> Url: https://github.com/boostorg/charconv/issues/110#issuecomment-1870268452  

I don't agree with your suggestion.

---

## Comment 5

> Username: mborland  
> Created at: 2024-01-04 09:34:04 UTC  
> Url: https://github.com/boostorg/charconv/issues/110#issuecomment-1876781024  

I think this is a valid concern.  
  
> 2. If WG21 ultimately chooses a different solution, you will bake the incompatibility with `std::from_chars` forever.  
>   
  
The intent was to go the way the committee goes: https://github.com/cppalliance/charconv/issues/37  
  
> Now, I understand that the JSON parser use case is that you want to treat super-small numbers as zero and super-huge values as Inf. I acknowledge that, but I would not spoil the clear interface of `from_chars` for that reason. By "clear" I mean:  
>   
> 1. Either there was an error or not: you can tell that from `ec`.  
> 2. If there was an error, the return value tells you what it was, and `value` remains unchanged.  
>   
> A solution acceptable by me would be to either expand `from_chars_result` to convey more information, or to allow the programmer to specify their expectations (such as the JSON parser use case) directly by passing dedicated function parameters.  
  
Adding additional members to our `from_chars_result` leaves us in the same place in terms of incompatibility that we are now.  
  
>   
> Of course that would make the interface of `boost::from_chars` depart from `std::from_chars`. Therefore, until [LWG 3081](https://cplusplus.github.io/LWG/lwg-active.html#3081) is resolved, I would propose to do two things:  
>   
> 1. Keep the contract of  `boost::from_chars` compliant with `std::from_chars`.  
> 2. Offer another function, like `from_chars_full_float_range`, that still has the "clear interface" as defined above and that returns values/errors as required by the JSON parser use case.  
  
Would you be amenable to a macro rather than adding another function? I think keeping our current behavior as the default and then if the user defines `BOOST_CHARCONV_STD_ERANGE` we change the behavior to what the STL does.

---

## Comment 6

> Username: akrzemi1  
> Created at: 2024-01-04 11:07:53 UTC  
> Url: https://github.com/boostorg/charconv/issues/110#issuecomment-1876915352  

The compatibility with `std` is a valid goal. I just note that the solution proposed in [LWG 3081](https://cplusplus.github.io/LWG/lwg-active.html#3081) is not official yet. So implementing it actually departs from the current `std`.  
  
I myself do not need a macro. Just pointing out the potential discrepancy.

---

## Comment 7

> Username: mborland  
> Created at: 2024-01-17 08:18:56 UTC  
> Url: https://github.com/boostorg/charconv/issues/110#issuecomment-1895306838  

This is going to be re-looked, and instead add additional functions to give the standard library behavior. From @Flamefire on the ML.
