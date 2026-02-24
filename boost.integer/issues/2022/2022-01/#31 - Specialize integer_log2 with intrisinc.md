# #31 - Specialize integer_log2 with intrisinc [Closed]

> Username: joprodrigues  
> Created at: 2022-01-15 17:59:26 UTC  
> Updated at: 2022-01-16 12:58:17 UTC  
> Closed at: 2022-01-16 12:58:17 UTC  
> Url: https://github.com/boostorg/integer/issues/31  

I think the integer_log2 can be replaced in with an intrisic approach. As far as I can tell, the current approach is not recognized by clang 11.0 as an idiom for integer_log2.
