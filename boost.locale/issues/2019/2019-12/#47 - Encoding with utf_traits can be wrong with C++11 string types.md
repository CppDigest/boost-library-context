# #47 - Encoding with utf_traits can be wrong with C++11 string types [Closed]

> Username: Flamefire  
> Created at: 2019-12-13 10:16:34 UTC  
> Updated at: 2023-03-04 16:57:02 UTC  
> Closed at: 2023-03-04 16:57:02 UTC  
> Url: https://github.com/boostorg/locale/issues/47  

The `utf_traits` class uses the size of the char to switch between UTF-8, UTF-16, UTF32, e.g. for `sizeof(CHAR)==2` it uses UTF-16: https://github.com/boostorg/locale/blob/develop/include/boost/locale/utf.hpp#L312  
  
C++11 introduced standardized strings with encoding via `char16_t, char32_t` (and later `char8_t`). Those are "at least" 2 or 4  bytes respectively wide. So using `utf_traits<char16_t>` might fail if that happens to be 4 bytes wide on some platform.  
  
Proposed solution: Templatize `utf_traits` over an encoding instead. This can be a plain-old-enum where values `UTF8, UTF16, UTF32` are set to `1, 2, 4` to allow this to be backwards compatible. Then dispatch (via an indirection if required) the `charN_t` types to their known encoding. Not sure if with the current impl one can directly make `utf_traits<char16_t, N>==utf_traits<char16_t, 2>` so indirection might be required.
