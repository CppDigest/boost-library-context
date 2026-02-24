# #129 - `boost::core::bit_width` accepts signed arguments in MSVC [Closed]

> Username: joaquintides  
> Created at: 2022-09-21 14:56:52 UTC  
> Updated at: 2022-09-21 19:11:44 UTC  
> Closed at: 2022-09-21 19:11:44 UTC  
> Url: https://github.com/boostorg/core/issues/129  

As shown [here](https://godbolt.org/z/fbKdjfadx). Moreover, the result is inconsistent with the documented behavior (for instance, `boost::core::bit_width(int(0))` returns -1).
