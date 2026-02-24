# #1789 Fix echo op test [Closed]

> Username: madmongo1  
> Created at: 2019-12-23 16:43:56 UTC  
> Updated at: 2023-01-21 06:36:13 UTC  
> Closed at: 2019-12-23 19:15:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1789  

echo_op test's move_only_handler has missing default constructor but defines move operators.  
Failed to compile on clang, c++2a

---
