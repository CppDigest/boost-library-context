# #241 - Remove iostream dependency [Open]

> Username: daniel-starke  
> Created at: 2025-06-06 20:49:07 UTC  
> Updated at: 2025-07-12 21:14:17 UTC  
> Url: https://github.com/boostorg/parser/issues/241  

The current implementation uses `std::ostream` in `detail::print_parser()` and `std::stringstream` in `parser.call()` for error reporing. For static linked executables this brings in the whole locale mashinary of STL. Something not always desirable. I suggest replacing it or at least make it customizable whether to use iostreams or not.  
  
I have made two examples with such a parser using `printf()` for error reporting:  
`lit('{') > int_ > lit(',') > int_ > lit('}')`  
  
Boost Spirit X3 results in a 154 KiB executable and Boost Parser in 218 KiB with error reporting removed in `parser.hpp` and 963 KiB with error reporting included.  
Both compiled with GCC 15.1 for MinGW-W64 using `-O2 -ffunction-sections -fdata-sections -s -static -Wl,--gc-sections`. LTO makes hardly any difference here.

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-07-12 21:14:17 UTC  
> Url: https://github.com/boostorg/parser/issues/241#issuecomment-3066050063  

I intend to do this once std::format is more widely available.  At the time I started this project, it wasn't implemented fully on any of the major implementations.  I won't be doing it soon, probably, but I do intend to address this in future.  Now that I know someone might want to use a non-iostream, non-format approach, I'll consider making that one of the customization options.
