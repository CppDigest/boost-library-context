# #56 - pool::max_chunks can not pass compile with marco max [Closed]

> Username: sevencolours24  
> Created at: 2024-02-24 02:01:25 UTC  
> Updated at: 2024-02-27 03:53:21 UTC  
> Closed at: 2024-02-27 03:53:21 UTC  
> Url: https://github.com/boostorg/pool/issues/56  

in boost/pool/pool.hpp line 363, function max_chunks(), call of std::numeric_limits<size_type>::max() will be replaced by marco max defined in minwindef.h on Windows, add () will fix this problem. like (std::numeric_limits<size_type>::max)()

---

## Comment 1

> Username: mclow  
> Created at: 2024-02-24 04:36:16 UTC  
> Url: https://github.com/boostorg/pool/issues/56#issuecomment-1965737640  

many of the boost libraries deal with this by defining `NOMINMAX`, thereby disabling those macros.  
Other libraries wrap the calls in parens, as you suggest.  
Still other libraries use `BOOST_PREVENT_MACRO_SUBSTITUTION` to handle this.  
Apparently pool does none of these.

---

## Comment 2

> Username: mclow  
> Created at: 2024-02-27 03:53:21 UTC  
> Url: https://github.com/boostorg/pool/issues/56#issuecomment-1965739731  

Turns out that Boost.Pool already uses both option 2 (wrap in parens) and option 3 (`BOOST_PREVENT_MACRO_SUBSTITUTION`), just not on that call.  
  
Fixed in ec7da07ed13e0c61e50d945b574a12ae7ec83cf4
