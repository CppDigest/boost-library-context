# #89 - boost::lexical_cast<unsigned short> to std::wstring produces incorrect output [Closed]

> Username: Vitek1425  
> Created at: 2025-09-18 17:36:51 UTC  
> Updated at: 2025-10-16 06:15:34 UTC  
> Closed at: 2025-10-16 06:15:34 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/89  

**Description:**  
When converting an `unsigned short` to `std::wstring` using `boost::lexical_cast`, the result is not the expected numeric string representation. Instead of `"4550"`, the conversion produces a single unexpected wide character.  
  
**Minimal Reproducible Example:**  
  
```cpp  
#include <boost/lexical_cast.hpp>  
#include <string>  
#include <iostream>  
  
int main() {  
    unsigned short number = 4550;  
  
    auto res1 = boost::lexical_cast<std::wstring>(number);  
    auto res2 = boost::lexical_cast<std::string>(number);  
  
    std::wcout << L"wstring: " << res1 << std::endl;  
    std::cout  << "string: " << res2 << std::endl;  
  
    return 0;  
}  
```  
  
**Observed behavior:**  
  
* `res1` contains the single character `ᇆ` (U+11C6).  
* `res2` correctly contains `"4550"`.  
  
**Expected behavior:**  
Both conversions should yield the same numeric representation as a string:  
  
* `res1 == L"4550"`  
* `res2 == "4550"`  
  
**Notes:**  
  
* The issue is reproducible with `unsigned short`.  
* Conversion with `int` works as expected.  
* In my setup, **“Treat `wchar_t` as Built-in Type” is disabled** (`/Zc:wchar_t-`).  
* This issue did **not** occur in Boost 1.81.0 (it appeared after upgrading to 1.88.0).  
  
**Environment:**  
  
* Boost version: 1.88.0 (works correctly in 1.81.0)  
* Compiler: MSVC 19.14.4 (Visual Studio 17.14.4)

---

## Comment 1

> Username: apolukhin  
> Created at: 2025-10-05 09:29:58 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/89#issuecomment-3368895694  

@Vitek1425 I have no acess to a compiler that supports `Zc:wchar_t- ` extension. I'd appreciate a PR with fix and test.  
  
Probably the code  
https://github.com/boostorg/lexical_cast/blob/8fc8a19931c8cb452400af907959fdacbbdd8ec1/include/boost/lexical_cast/detail/converter_lexical_streams.hpp#L297-L299  
should be wrapped around with `#if !defined(BOOST_LCAST_NO_WCHAR_T)` and `#endif`.  
  
Could you please check that?
