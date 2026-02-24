# #89 add `clang-format off` guard on space-sensitive macros [Closed]

> Username: shyeyian  
> Created at: 2025-08-20 06:44:30 UTC  
> Updated at: 2025-08-28 09:23:34 UTC  
> Closed at: 2025-08-26 08:47:46 UTC  
> Url: https://github.com/boostorg/mpl/pull/89  

`clang-format` formats `AUX778076_INCLUDE_DIR/BOOST_MPL_PREPROCESSED_HEADER` into `AUX778076_INCLUDE_DIR / BOOST_MPL_PREPROCESSED_HEADER` (in file `boost/mpl/vector/aux_/include_preprocessed.hpp` etc) and causes some tiny errors. I wish we could add `clang-format off` guard in these places.  
  
**Thank you! This is an amazing library and I will star it :)**

---
