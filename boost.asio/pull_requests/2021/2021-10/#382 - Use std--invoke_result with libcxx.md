# #382 Use std::invoke_result with libcxx [Closed]

> Username: ecatmur  
> Created at: 2021-10-11 07:30:42 UTC  
> Updated at: 2021-10-11 07:31:15 UTC  
> Closed at: 2021-10-11 07:31:14 UTC  
> Url: https://github.com/boostorg/asio/pull/382  

std::result_of is removed in clang 13 libcxx in C++20 mode per https://github.com/llvm/llvm-project/commit/2ff5a56e1ab2a95c36d3c5d2bef7c585125718ae  
std::invoke_result is available in libcxx since clang 7 given _LIBCPP_STD_VER > 14 (this is the form of the check in libcxx <type_traits>)  
  
The source code in this repository is generated from an upstream repository at https://github.com/chriskohlhoff/asio.  
  
Please consider raising new pull requests at https://github.com/chriskohlhoff/asio/pulls.

---

## Comment 1

> Username: ecatmur  
> Created_at: 2021-10-11 07:31:14 UTC  
> Url: https://github.com/boostorg/asio/pull/382#issuecomment-939764806  

Closing per https://github.com/chriskohlhoff/asio/pull/910

---
