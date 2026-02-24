# #249 - BOOST_CHARCONV_QUADMATH_FOUND should be a usage-requirement in the Jamfile, too [Closed]

> Username: anarthal  
> Created at: 2025-01-01 13:40:14 UTC  
> Updated at: 2025-01-03 18:18:25 UTC  
> Closed at: 2025-01-03 18:18:25 UTC  
> Url: https://github.com/boostorg/charconv/issues/249  

[This](https://github.com/boostorg/charconv/blob/8fbdb8a08bf0d926b61dfdfea2872f019c8d3608/build/Jamfile#L27) only applies the macro definition to the library source files, and not to consumers. This makes tests like [this](https://github.com/boostorg/charconv/blob/develop/test/test_float128.cpp) to always be skipped. I can submit a PR to fix this, if you like.

---

## Comment 1

> Username: mborland  
> Created at: 2025-01-02 13:53:48 UTC  
> Url: https://github.com/boostorg/charconv/issues/249#issuecomment-2567810484  

Does this need to get fixed too then: https://github.com/boostorg/charconv/blob/develop/CMakeLists.txt#L52? PR would be appreciated.

---

## Comment 2

> Username: anarthal  
> Created at: 2025-01-02 15:42:35 UTC  
> Url: https://github.com/boostorg/charconv/issues/249#issuecomment-2567971449  

I don't think so. What I'm proposing is actually changing the Jamfile so it does the same as the CMake (which today it doesn't).  
  
As it's written  
* The Jamfile applies the macro definition only to the .cpp files in the library, not to its consumers.  
* The CMake applies the macro definition to both thr .cpp files in the library and to its consumers (`PUBLIC`). I think this is the correct behaviour. The current Jamfile's behaviour is equivalent to CMake's `PRIVATE`.  
  
I will PR with a fix

---

## Comment 3

> Username: mborland  
> Created at: 2025-01-02 15:52:31 UTC  
> Url: https://github.com/boostorg/charconv/issues/249#issuecomment-2567986638  

Sounds good. Thank you.
