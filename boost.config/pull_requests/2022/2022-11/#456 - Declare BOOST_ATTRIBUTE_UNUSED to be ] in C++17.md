# #456 Declare BOOST_ATTRIBUTE_UNUSED to be [[maybe_unused]] in C++17 [Closed]

> Username: mojca  
> Created at: 2022-11-15 22:22:45 UTC  
> Updated at: 2022-11-16 19:29:46 UTC  
> Closed at: 2022-11-16 19:29:46 UTC  
> Url: https://github.com/boostorg/config/pull/456  

See https://github.com/boostorg/serialization/issues/271 and https://github.com/boostorg/serialization/issues/145  
  
I'm getting a compiler warning (or rather error since I try to compile with "treat warnings as errors") in Visual Studio 2022.  
  
Since this became a standard in C++17 (https://en.cppreference.com/w/cpp/language/attributes/maybe_unused), it might be worth using that one. The compiler warning disappears.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-11-16 19:29:45 UTC  
> Url: https://github.com/boostorg/config/pull/456#issuecomment-1317560527  

I fixed this slightly differently in the referenced PR.

---
