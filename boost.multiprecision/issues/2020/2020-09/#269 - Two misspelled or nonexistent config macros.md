# #269 - Two misspelled or nonexistent config macros [Closed]

> Username: jefftrull  
> Created at: 2020-09-04 05:18:29 UTC  
> Updated at: 2020-09-04 08:24:45 UTC  
> Closed at: 2020-09-04 08:24:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/269  

In `test_arithmetic.hpp` the code refers to `BOOST_NO_CXX11_LONG_LONG`, which does not exist - perhaps `BOOST_NO_LONG_LONG` is meant?

---

## Comment 1

> Username: jefftrull  
> Created at: 2020-09-04 05:22:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/269#issuecomment-686914834  

Also in `config/has_is_constant_evaluated.cpp` there is a reference to `BOOST_NO_CXX17_CONSTEXPR`, which does not seem to exist.
