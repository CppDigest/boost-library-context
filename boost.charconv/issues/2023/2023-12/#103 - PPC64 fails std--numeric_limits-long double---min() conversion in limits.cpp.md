# #103 - PPC64 fails std::numeric_limits<long double>::min() conversion in limits.cpp [Closed]

> Username: mborland  
> Created at: 2023-12-07 10:15:38 UTC  
> Updated at: 2023-12-07 12:40:28 UTC  
> Closed at: 2023-12-07 12:40:28 UTC  
> Url: https://github.com/boostorg/charconv/issues/103  

`from_chars()` returns `std::errc::result_out_of_range`, but the correct value. `to_chars()` returns the correct value and ec.
