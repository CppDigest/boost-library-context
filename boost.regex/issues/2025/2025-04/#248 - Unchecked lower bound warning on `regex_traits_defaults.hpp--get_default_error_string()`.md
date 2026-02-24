# #248 - Unchecked lower bound warning on `regex_traits_defaults.hpp::get_default_error_string()` [Open]

> Username: ealva  
> Created at: 2025-04-11 18:51:58 UTC  
> Updated at: 2025-07-10 23:27:36 UTC  
> Url: https://github.com/boostorg/regex/issues/248  

Flagged by MSVC as [C33010](https://learn.microsoft.com/en-us/cpp/code-quality/c33010?view=msvc-170) (there might be others in the same file).

---

## Comment 1

> Username: oldnewthing  
> Created at: 2025-07-10 23:27:36 UTC  
> Url: https://github.com/boostorg/regex/issues/248#issuecomment-3059513192  

If error_type is negative, then the code indexes beyond the beginning of the array. The range check needs to check for "less than error_ok" or cast to uint before checking the upper bound.
