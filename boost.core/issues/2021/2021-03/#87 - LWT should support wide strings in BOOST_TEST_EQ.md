# #87 - LWT should support wide strings in BOOST_TEST_EQ [Open]

> Username: pdimov  
> Created at: 2021-03-10 22:53:24 UTC  
> Updated at: 2021-03-11 10:17:35 UTC  
> Url: https://github.com/boostorg/core/issues/87  

Outputting wide strings to `std::cout` outputs the `void*` under C++ before 20, and hits a deleted `op<<` in C++20. We should make it work, either by transcoding to UTF-8, or by using the `codecvt` facet of the output stream.  
  
This might entail adding an adapter such as `boost::core::narrow(x)` that is similar to `quoted(x)` in that when output to a stream, it does the necessary narrowing using `codecvt`. (We can't add it to `io` because Core is not allowed to depend on Io.)  
  
Related: we don't have a wide equivalent of `BOOST_TEST_CSTR_EQ`.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-03-11 10:12:41 UTC  
> Url: https://github.com/boostorg/core/issues/87#issuecomment-796625127  

> We should make it work, either by transcoding to UTF-8, or by using the `codecvt` facet of the output stream.  
  
I have something like this in Boost.Log:  
  
https://github.com/boostorg/log/blob/8a880720be5a990fdd0f864244bfff1a39a69d83/include/boost/log/utility/formatting_ostream.hpp#L398-L416  
https://github.com/boostorg/log/blob/8a880720be5a990fdd0f864244bfff1a39a69d83/src/code_conversion.cpp#L68-L139  
  
I'm not sure we want that particular solution, instead of just outputting to `std::wcout`.

---

## Comment 2

> Username: Lastique  
> Created at: 2021-03-11 10:17:34 UTC  
> Url: https://github.com/boostorg/core/issues/87#issuecomment-796628108  

> We can't add it to `io` because Core is not allowed to depend on Io.  
  
I don't like that we're adding stuff to Core just because we don't want to add it to the module where it really belongs. Code reuse must work, however modular we want to be.
