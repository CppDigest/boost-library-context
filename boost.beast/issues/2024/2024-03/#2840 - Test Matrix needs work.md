# #2840 - Test Matrix needs work [Closed]

> Username: vinniefalco  
> Created at: 2024-03-20 17:15:40 UTC  
> Updated at: 2024-08-06 13:21:12 UTC  
> Closed at: 2024-08-06 13:21:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2840  

A lot of suboptimal outcomes here  
https://www.boost.org/development/tests/master/developer/beast.html  
  
Compare:  
https://www.boost.org/development/tests/master/developer/url.html

---

## Comment 1

> Username: ashtum  
> Created at: 2024-08-05 13:10:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2840#issuecomment-2269042147  

This is addressed in: https://github.com/boostorg/beast/pull/2903, https://github.com/boostorg/beast/pull/2877, and https://github.com/boostorg/beast/pull/2846.  
  
Here are the remaining errors in the matrix that are unrelated to Beast:  
- The compile errors on riscv64 machines are due to timeouts caused by insufficient memory and reliance on swap. I can compile and test the code successfully on a riscv64 machine running in QEMU.  
- The `clang-linux-6.0~c++17~lc` errors are because Asio doesn't check for the existence of `std::invoke_result` in the standard library. I've submitted a pull request to address this: [asio PR #1498](https://github.com/chriskohlhoff/asio/pull/1498).  
- The `C2955` and `C2066` errors in `msvc-14.0` are originating from the `spawn.hpp` file in Asio. I've reported this issue: https://github.com/chriskohlhoff/asio/issues/1488.
