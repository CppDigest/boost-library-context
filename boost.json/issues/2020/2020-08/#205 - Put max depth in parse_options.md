# #205 - Put max depth in parse_options [Closed]

> Username: vinniefalco  
> Created at: 2020-08-25 14:38:24 UTC  
> Updated at: 2020-08-27 02:41:24 UTC  
> Closed at: 2020-08-27 02:41:24 UTC  
> Url: https://github.com/boostorg/json/issues/205  

it makes sense there, and then `parser` doesn't have to provide the member function. But then we need a way to query the parse options on the `parser` (and `basic_parser`).
