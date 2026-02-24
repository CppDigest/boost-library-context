# #19 - Provide analogue of `std::numbers` from C++20 [Open]

> Username: denzor200  
> Created at: 2025-03-18 22:54:21 UTC  
> Updated at: 2025-03-18 22:54:21 UTC  
> Url: https://github.com/boostorg/compat/issues/19  

https://en.cppreference.com/w/cpp/header/numbers  
  
Basically we will provide only `inline constexpr double` variables - like `e`, `pi`, `log2e`, ...  
For C++14 we can additionally provide `template<class T> inline constexpr T` - like `e_v`, `pi_v`, `log2e_v`, ...
