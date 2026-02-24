# #450 - Exceptions in `basic_parser` derived class [Closed]

> Username: vinniefalco  
> Created at: 2017-06-09 14:28:02 UTC  
> Updated at: 2018-04-27 02:40:14 UTC  
> Closed at: 2018-04-27 02:40:14 UTC  
> Url: https://github.com/boostorg/beast/issues/450  

Maybe `basic_parser` should catch `std::bad_alloc` and covert it to `http::error::bad_alloc`? This would make it easier for parsers and allocators that have memory limits.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2018-04-24 17:52:39 UTC  
> Url: https://github.com/boostorg/beast/issues/450#issuecomment-384022650  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-04-27 02:40:10 UTC  
> Url: https://github.com/boostorg/beast/issues/450#issuecomment-384846739  

On second thought this is not a good idea because it is standard C++ practice to have functions which return `error_code` and also throw on `bad_alloc`, this was the topic of a recent discussion on the LWG reflector.
