# #733 - heap-buffer-overflow when using io-related functions [Closed]

> Username: Osyotr  
> Created at: 2023-04-12 14:24:09 UTC  
> Updated at: 2024-05-13 07:59:01 UTC  
> Closed at: 2024-05-13 07:59:01 UTC  
> Url: https://github.com/boostorg/gil/issues/733  

### Actual behavior  
  
Writing images and views to files does not work because string conversion functions are not implemented properly:  
https://github.com/boostorg/gil/blob/eabd679f632be3170d98145fcc3b49e85df7cc4b/include/boost/gil/io/path_spec.hpp#L90-L97  
The code above does not work for paths that contain non-ascii symbols. The string it produces does not have `\0` at the end.  
  
### Expected  behavior  
  
Passing `std::filesystem::path` or `std::wstring` to io-related functions should work properly.  
Possible solution is to remove explicit string conversions, construct `std::filesystem::path` and use its `.string()` method.  
Note that it may break on windows because of usage of `fopen` https://github.com/boostorg/gil/blob/eabd679f632be3170d98145fcc3b49e85df7cc4b/include/boost/gil/io/device.hpp#L105 (`_wfopen` should be used on windows)  
  
  
### C++ Minimal Working Example  
  
I've extracted broken part (linked above) to reproduce the issue: https://godbolt.org/z/rvxsPG7a4  
  
```cpp  
#include <boost/gil.hpp>  
#include <filesystem>  
namespace gil = boost::gil;  
int main  
{  
    std::filesystem::path path = L"/some_path/傳/привет/qwerty";  
    gil::bgra8_image_t image;  
    gil::write_view(path, gil::const_view(image));  
}  
```  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Compiler version: GCC 11.2  
- Build settings: None  
- Version: 1.80

---

## Comment 1

> Username: striezel  
> Created at: 2024-05-05 16:11:46 UTC  
> Url: https://github.com/boostorg/gil/issues/733#issuecomment-2094864132  

Yes, this is a bug.  
  
The length calculation is wrong. The code uses `wcslen()` to "calculate" the length of the converted string, however `wcslen()` is just a `strlen()` for `wchar_t*` strings. So it returns the number of wide characters in a `wchar_t*` string, but those do not need to be the same as the number of narrow characters (that is: `char`) in the converted string. It just happens to be the same, if the wide character string only uses characters of the English alphabet where each wide character is converted to exactly one narrow character.  
  
Instead of `wcslen()` the code could use `wcstombs()`'s POSIX extension to calculate the length before doing the conversion.  
  
> POSIX specifies a common extension: if `dst` is a null pointer, this function returns the number of bytes that would be written to `dst`, if converted.  
  
But that is an extension, so we should probably use [`wcsrtombs()`](https://en.cppreference.com/w/cpp/string/multibyte/wcsrtombs) instead, where that behaviour is not an extension but part of the function and we do not need to rely on the presence of an extension.  
  
Long story short: I'll try to prepare a fix.
