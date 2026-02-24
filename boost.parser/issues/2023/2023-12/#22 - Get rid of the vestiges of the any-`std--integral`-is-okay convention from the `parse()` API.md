# #22 - Get rid of the vestiges of the any-`std::integral`-is-okay convention from the `parse()` API [Closed]

> Username: tzlaine  
> Created at: 2023-12-02 23:24:34 UTC  
> Updated at: 2023-12-03 22:13:49 UTC  
> Closed at: 2023-12-03 22:13:49 UTC  
> Url: https://github.com/boostorg/parser/issues/22  

Currently, parseable means any `std::integral` of size 1 or 4.  We only want to allow character types `char`, `wchar_t` (non-MSVC), `char8_t`, and `char32_t`.
