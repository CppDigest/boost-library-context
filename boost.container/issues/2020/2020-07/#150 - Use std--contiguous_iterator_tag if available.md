# #150 - Use std::contiguous_iterator_tag if available [Closed]

> Username: patlefort  
> Created at: 2020-07-05 15:30:23 UTC  
> Updated at: 2021-04-20 22:46:02 UTC  
> Closed at: 2021-04-20 22:43:41 UTC  
> Url: https://github.com/boostorg/container/issues/150  

vector's iterator should provide a std::iterator_traits specialization with iterator_concept = std::contiguous_iterator_tag if it's available. Otherwise, it is for example unusable with std::span if you pass it as a range.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-04-20 22:46:02 UTC  
> Url: https://github.com/boostorg/container/issues/150#issuecomment-823646013  

Thanks for the request. Tested in GCC and MSVC, it will surely need additional tweaks for Clang/C++ depending on LWG3446 resolution implementation in libc++.
