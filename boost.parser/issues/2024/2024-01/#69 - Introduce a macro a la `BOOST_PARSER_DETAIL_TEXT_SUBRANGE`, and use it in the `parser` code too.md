# #69 - Introduce a macro a la `BOOST_PARSER_DETAIL_TEXT_SUBRANGE`, and use it in the `parser` code too [Closed]

> Username: tzlaine  
> Created at: 2024-01-16 05:40:40 UTC  
> Updated at: 2024-01-17 08:12:32 UTC  
> Closed at: 2024-01-17 08:12:32 UTC  
> Url: https://github.com/boostorg/parser/issues/69  

This is to ensure that we return a `std::ranges::subrange` in C++20 and later.
