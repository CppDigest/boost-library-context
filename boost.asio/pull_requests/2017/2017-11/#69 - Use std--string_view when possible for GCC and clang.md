# #69 Use std::string_view when possible for GCC and clang. [Closed]

> Username: Jiwan  
> Created at: 2017-11-26 20:45:24 UTC  
> Updated at: 2017-12-02 07:31:48 UTC  
> Closed at: 2017-12-02 07:31:48 UTC  
> Url: https://github.com/boostorg/asio/pull/69  

With GCC 7.x /libstdc++ and clang 4.x/libc++ supporting C++17, it would be nice to use ``std::string_view`` instead of ``std::experimental::string_view``.  
  
I don't like much the meaning of ``BOOST_ASIO_HAS_STD_EXPERIMENTAL_STRING_VIEW``, I would rather having it named ``BOOST_ASIO_USE_STD_EXPERIMENTAL_STRING_VIEW`` or ``BOOST_ASIO_FALLBACK_STD_EXPERIMENTAL_STRING_VIEW``, but that would imply changing ``<boost/asio/detail/string_view.hpp>``.

---

## Comment 1

> Username: Jiwan  
> Created_at: 2017-11-26 20:49:38 UTC  
> Updated_at: 2017-11-26 20:50:38 UTC  
> Url: https://github.com/boostorg/asio/pull/69#issuecomment-347037282  

As a side question, I would like to know if the header ``<boost/asio/detail/string_view.hpp>`` could be used by another boost library (in this case ``Boost.Beast``) or if the ``detail`` folder can have breaking changes anytime. It would be really useful if ``Boost.Beast`` shares the same ``string_view`` implementation.

---

## Comment 2

> Username: chriskohlhoff  
> Created_at: 2017-12-02 07:31:48 UTC  
> Url: https://github.com/boostorg/asio/pull/69#issuecomment-348674820  

`std::experimental::string_view` is currently preferred because the networking TS is specified to use it. In the future I will investigate supporting both at the same time.

---
