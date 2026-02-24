# #163 - Boost.Integer test failures on clang-9 in C++03 mode [Closed]

> Username: Lastique  
> Created at: 2019-10-23 09:30:09 UTC  
> Updated at: 2019-11-24 12:00:13 UTC  
> Closed at: 2019-11-24 12:00:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/163  

See here:  
  
https://travis-ci.org/boostorg/integer/jobs/601250569#L774  
  
There are multiple problems:  
  
1. Using unified initialization syntax in https://github.com/boostorg/multiprecision/blob/f59ac5056e93872849df701b9a55010bc367a64c/include/boost/multiprecision/cpp_int.hpp#L474 without a check for `BOOST_NO_CXX11_UNIFIED_INITIALIZATION_SYNTAX`.  
2. Using `constexpr` in https://github.com/boostorg/multiprecision/blob/f59ac5056e93872849df701b9a55010bc367a64c/include/boost/multiprecision/cpp_int.hpp#L532 and several other places instead of `BOOST_CONSTEXPR`.  
3. Using `constexpr` in https://github.com/boostorg/multiprecision/blob/f59ac5056e93872849df701b9a55010bc367a64c/include/boost/multiprecision/cpp_int/bitwise.hpp#L622 instead of `BOOST_CONSTEXPR_OR_CONST`.  
  
Maybe there are more problems, the above ones are those made immediately apparent by Boost.Integer test failures.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-10-23 17:48:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/163#issuecomment-545559804  

Reproduced.  
  
The sections of code you highlight **are** guarded for the presence of the C++20 feature `std::is_constant_evaluated()`, but apparently clang's __has_builtin is signalling the presence of the feature even in C++03 :(  
  
Testing fix now.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-11-24 12:00:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/163#issuecomment-557881928  

This should be fixed in release now
