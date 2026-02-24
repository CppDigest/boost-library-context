# #244 - Rounding in parser doesn't work/is not used [Open]

> Username: Flamefire  
> Created at: 2024-12-28 15:11:01 UTC  
> Updated at: 2024-12-29 14:03:17 UTC  
> Url: https://github.com/boostorg/charconv/issues/244  

The code to check for rounding isn't tested: https://github.com/boostorg/charconv/blob/8fbdb8a08bf0d926b61dfdfea2872f019c8d3608/include/boost/charconv/detail/parser.hpp#L314-L326  
  
I guess the initial idea was:  
  
- Use a buffer 1 **larger** than the max significant digits (see also https://github.com/boostorg/charconv/pull/243)  
- If the buffer was full i.e. `offset == significand_buffer_size` (can it ever be greater?) use the last digit for rounding  
  
This IMO needs an additional overflow check in the rounding (the `+=1`)

---

## Comment 1

> Username: Flamefire  
> Created at: 2024-12-29 14:03:16 UTC  
> Url: https://github.com/boostorg/charconv/issues/244#issuecomment-2564735262  

Upon further inspection I changed the title to reflect that this rounding is not used and not only not tested, see https://app.codecov.io/gh/boostorg/charconv/blob/develop/include%2Fboost%2Fcharconv%2Fdetail%2Fparser.hpp#L316  
  
Additionally I think it doesn't even do what it should:  
  
- The `parser` interface seems to be used only for (some) `long double` and by `from_chars_float_impl`  if the format is "hex"  
- for the latter the rounding doesn't make sense as the significant buffer is sized for base 10 so if that is full, with base 16 the value will be too large returning an [out-of-range later](https://github.com/boostorg/charconv/blob/8fbdb8a08bf0d926b61dfdfea2872f019c8d3608/include/boost/charconv/detail/parser.hpp#L207)  
- for the `long double` case (which is only used on some platforms, e.g. not for MSVC or if `long double == double`) the rounding case cannot be entered because `i <= significand_buffer_size` is ensured by the prior code  
  
That might actually introduce a rounding error violating the specification: E.g. "Rounding" the `uint64` significant to `double` (53bit resolution) where remaining decimal digits are ignored instead of used for round can lead to a different rounding for some values as the rounding to the last digit of the significant might cause a carry to the place used for rounding to double,
