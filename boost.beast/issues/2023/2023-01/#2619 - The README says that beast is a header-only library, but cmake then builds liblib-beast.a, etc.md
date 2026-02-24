# #2619 - The README says that beast is a header-only library, but cmake then builds liblib-beast.a, etc [Closed]

> Username: yurivict  
> Created at: 2023-01-12 04:25:05 UTC  
> Updated at: 2024-01-03 08:05:08 UTC  
> Closed at: 2024-01-03 08:05:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2619  



---

## Comment 1

> Username: ashtum  
> Created at: 2024-01-03 08:05:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2619#issuecomment-1874978883  

@yurivict Beast is a header-only library, you can include the Beast header and compile your application. However, Beast is designed in a way that separates non-templated implementations into `.ipp` files, enabling you to build them once and reuse them. Simply include `boost/beast/src.hpp` in a separate translation unit and define the [BOOST_BEAST_SEPARATE_COMPILATION](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/config/configuration_preprocessor_defin.html) macro before including Beast headers to enhance compile time.
