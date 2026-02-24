# #416 - Use NullablePointer / OptionalLength concept [Closed]

> Username: vinniefalco  
> Created at: 2017-06-03 20:30:07 UTC  
> Updated at: 2018-11-28 01:09:18 UTC  
> Closed at: 2018-11-28 01:09:18 UTC  
> Url: https://github.com/boostorg/beast/issues/416  

Instead of `boost::optional<std::uint64_t>` for user defined functions in concepts, the library should define the *OptionalLength* concept.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-11-28 01:09:18 UTC  
> Url: https://github.com/boostorg/beast/issues/416#issuecomment-442279636  

I'm not really feeling this to be honest. Maybe in a C++17 or C++20 version of Beast that uses language-based Concepts.
