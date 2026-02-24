# #124 - convert asicii to utf8 [Closed]

> Username: ghost  
> Created at: 2021-02-06 02:19:44 UTC  
> Updated at: 2021-02-06 06:01:41 UTC  
> Closed at: 2021-02-06 05:55:11 UTC  
> Url: https://github.com/boostorg/nowide/issues/124  

if i have a string  
  
std::string str1 = "你好";  
  
how can i convert it to utf8 and get  
  
std::string str2 = SomeConvert(str1);  
  
where str2 = "\xbb\xbd\xa0\xe5\xa5\xbd"

---

## Comment 1

> Username: Berrysoft  
> Created at: 2021-02-06 05:43:14 UTC  
> Updated at: 2021-02-06 06:01:41 UTC  
> Url: https://github.com/boostorg/nowide/issues/124#issuecomment-774405081  

For C++ 11, 14, 17, and 20 without `char8_t` enabled, you can just write  
``` cpp  
std::string str2 = u8"你好";  
```  
For C++ 20 with `char8_t` enabled, and with this repo, you can write  
``` cpp  
// #include <boost/nowide/convert.hpp>  
std::string str2 = boost::nowide::narrow(std::wstring_view(L"你好"));  
```  
For C++ 20 with `char8_t` enabled, if you don't care about *undefined behivor*, it is OK:  
```cpp  
std::string str2 = (const char*)u8"你好";  
```  
**But**, if there's an ascii string with current code page(let's suppose you're on Windows), and you may not know it at compile time, you should use Windows API to do so. Basically, ASCII -> UTF16 -> UTF8:  
``` cpp  
// #include <Windows.h>  
std::string str1 = "你好";  
// CP_ACP deals with current code page currectly  
int len = MultiByteToWideChar(CP_ACP, 0, str1.c_str(), (int)str1.size(), nullptr, 0);  
std::wstring temp_str((std::size_t)len, L'\0');  
// It uses C++ 17. If the version is lower, write this:  
// MultiByteToWideChar(CP_ACP, 0, str1.c_str(), (int)str1.size(), const_cast<wchar_t*>(temp_str.data()), len);  
MultiByteToWideChar(CP_ACP, 0, str1.c_str(), (int)str1.size(), temp_str.data(), len);  
len = WideCharToMultiByte(CP_UTF8, 0, temp_str.c_str(), (int)temp_str.size(), nullptr, 0, 0, nullptr);  
std::string str2(len, '\0');  
// WideCharToMultiByte(CP_UTF8, 0, temp_str.c_str(), (int)temp_str.size(), const_cast<char*>(str2.data()), len, 0, nullptr);  
WideCharToMultiByte(CP_UTF8, 0, temp_str.c_str(), (int)temp_str.size(), str2.data(), len, 0, nullptr);  
```  
In addition, there's MSVC extension to do so, if you don't like Windows API. But you should know your current code page, for example, <del>GB2312</del>CP936(GBK, a superset of GB2312):  
``` cpp  
// #include <locale>  
// #include <codecvt>  
// #include <cvt/cp936>  
std::string str1 = "你好";  
// Note that wstring_convert is deprecated after C++ 17, but generally you needn't care about that.  
std::wstring_convert<stdext::cvt::codecvt_cp936<wchar_t>> cvt;  
std::wstring temp_str = cvt.from_bytes(str1);  
std::wstring_convert<std::codecvt_utf8_utf16<wchar_t>> cvt2;  
std::string str2 = cvt2.to_bytes(temp_str);  
```

---

## Comment 2

> Username: ghost  
> Created at: 2021-02-06 05:55:11 UTC  
> Url: https://github.com/boostorg/nowide/issues/124#issuecomment-774406369  

thanks for your answer! its very detailed
