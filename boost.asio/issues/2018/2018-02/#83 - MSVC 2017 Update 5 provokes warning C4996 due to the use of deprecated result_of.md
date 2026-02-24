# #83 - MSVC 2017 Update 5 provokes warning C4996 due to the use of deprecated result_of [Closed]

> Username: mkurdej  
> Created at: 2018-02-12 09:33:52 UTC  
> Updated at: 2020-12-30 00:48:51 UTC  
> Closed at: 2020-12-30 00:48:35 UTC  
> Url: https://github.com/boostorg/asio/issues/83  

`std::result_of` and `std::result_of_t` are deprecated in C++17 and `std::invoke_result`, `std::invoke_result_t` should be used instead.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:48:17 UTC  
> Url: https://github.com/boostorg/asio/issues/83#issuecomment-752288924  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#598](https://github.com/chriskohlhoff/asio/issues/598).
