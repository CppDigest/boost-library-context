# #136 - boost::nowide::cout cannot output std::filesystem::path correctly [Closed]

> Username: u8cat  
> Created at: 2021-08-13 14:29:32 UTC  
> Updated at: 2025-05-26 09:07:41 UTC  
> Closed at: 2023-03-04 15:48:26 UTC  
> Url: https://github.com/boostorg/nowide/issues/136  

Code:  
```c++  
#include <boost/nowide/iostream.hpp>  
#include <filesystem>  
namespace fs=std::filesystem;  
  
int main()  
{  
	try  
	{  
		boost::nowide::cout<<"use c_str()\n";  
		for(auto &i:fs::directory_iterator("."))  
			boost::nowide::cout<<i.path().filename().c_str()<<"\n";  
	}  
	catch(std::exception &err)  
	{  
		boost::nowide::cout<<err.what()<<"\n";  
	}  
	try  
	{  
		boost::nowide::cout<<"output directly\n";  
		for(auto &i:fs::directory_iterator("."))  
			boost::nowide::cout<<i.path().filename()<<"\n";  
	}  
	catch(std::exception &err)  
	{  
		boost::nowide::cout<<err.what()<<"\n";  
	}  
	try  
	{  
		boost::nowide::cout<<"use string()\n";  
		for(auto &i:fs::directory_iterator("."))  
			boost::nowide::cout<<i.path().filename().string()<<"\n";  
	}  
	catch(std::exception &err)  
	{  
		boost::nowide::cout<<err.what()<<"\n";  
	}  
	/*  
	error C2679: binary '<<': no operator found which takes a right-hand operand of type 'std::wstring' (or there is no acceptable conversion)  
	*/  
//	try  
//	{  
//		boost::nowide::cout<<"use wstring()\n";  
//		for(auto &i:fs::directory_iterator("."))  
//			boost::nowide::cout<<i.path().filename().wstring()<<"\n";  
//	}  
//	catch(std::exception &err)  
//	{  
//		boost::nowide::cout<<err.what()<<"\n";  
//	}  
	return 0;  
}  
```  
  
Compiler:  
Microsoft (R) C/C++ Optimizing Compiler Version 19.28.29337 for x64  
  
Boost version: 1.75  
  
Compilation command:  
`cl dir.cpp /EHsc /MD /utf-8 /std:c++17 /link`  
  
I put a file named `War and Peace - Война и мир - מלחמה ושלום.zip` and the executable file `dir.exe` in the same directory, and run it on a computer whose codepage is 936 (Simplified Chinese, GBK).  
![fig2](https://user-images.githubusercontent.com/48008292/129361691-79529221-75cf-46c2-99fe-e6d17e1be590.png)  
  
I tried four ways to output the path in my program, but none of them works:  
![fig1](https://user-images.githubusercontent.com/48008292/129361489-00ac3ae7-9651-4678-aa6b-c9e1d68daad7.png)  
![fig3](https://user-images.githubusercontent.com/48008292/129370659-8eb6320d-3719-428c-b2b0-84e93e0918e6.png)  
(The text “在多字节的目标代码中，没有此 Unicode 字符可以映射到的字符。” is the std::exception::what()'s message and is encoded in GBK.)  
  
I know the problem can be fixed by simply switching std::filesystem to boost::filesystem, however, many people (including me) prefer the standalone version of Nowide, besides, using the C++ standard library is sometimes considered to be more proper and portable.  
  
Fixing this bug is challenging as the narrow string's encoding of std::filesystem::path cannot be changed. One possible way is to add the following functions:   
1. `boost::nowide::detail::winconsole_istream& operator<<(boost::nowide::detail::winconsole_istream&,const std::filesystem::path&) `  
2. `boost::nowide::detail::winconsole_istream& operator<<(boost::nowide::detail::winconsole_istream&,const wchar_t*) `  
3. `boost::nowide::basic_fstream<CharType>& operator<<(boost::nowide::basic_fstream<CharType>&,const std::filesystem::path&)`  
4. `boost::nowide::basic_fstream<CharType>& operator<<(boost::nowide::basic_fstream<CharType>&,const wchar_t*)`  
  
Notice that the `wchar_t` here is **unavoidable** because `c_str()` is the only way to output the “*native pathname format*” rather than the “*generic pathname format*” without explicitly using “wide string” (otherwise, you have to use `wstring()`/`u16string()`/`u32string()`, which are “wide strings”; or use `u8string()`, which is not available in C++ 17).

---

## Comment 1

> Username: Flamefire  
> Created at: 2021-08-16 09:36:15 UTC  
> Url: https://github.com/boostorg/nowide/issues/136#issuecomment-899369189  

> Fixing this bug is challenging as the narrow string's encoding of std::filesystem::path cannot be changed. One possible way is to add the following functions:  
  
This is not exactly true. For boost::filesystem this is done here: https://github.com/boostorg/nowide/blob/develop/include/boost/nowide/filesystem.hpp I'd suggest to do the same for your case.  
  
The major problem here is: the fs::path stream operators are provided by fs::path itself. I'd rather not add own versions which would need to exactly match standard behavior.  
  
So simply imbue your paths with an UTF-8 locale and use UTF-8 everywhere (which is the intention of this library)

---

## Comment 2

> Username: u8cat  
> Created at: 2021-08-18 14:54:16 UTC  
> Url: https://github.com/boostorg/nowide/issues/136#issuecomment-901183375  

Thanks for your reply.  
  
> This is not exactly true. For boost::filesystem this is done here: https://github.com/boostorg/nowide/blob/develop/include/boost/nowide/filesystem.hpp I'd suggest to do the same for your case.  
  
For `boost::filesystem`, `path::imbue()`  works, however, unfortunately `std::filesystem::path` does **not** contain the member `imbue`. Futhermore, none of these functions works:  
```c++  
// error C2039: 'imbue': is not a member of 'std::filesystem::path'  
//std::filesystem::path::imbue(std::locale(std::locale(), new boost::nowide::utf8_codecvt<wchar_t>()));  
  
// dosen't work  
std::locale::global(std::locale(std::locale(), new boost::nowide::utf8_codecvt<wchar_t>()));  
  
// doesn't work  
boost::nowide::cout.imbue(std::locale(std::locale(), new boost::nowide::utf8_codecvt<wchar_t>()));  
```  
  
After (not so) carefully read Microsoft domcuments, I found an extraordinary function (to be exact, a statement):  
```c  
setlocale(LC_ALL,".UTF-8");  
```  
Microsoft [claims](https://docs.microsoft.com/en-us/cpp/c-runtime-library/reference/setlocale-wsetlocale?view=msvc-160#utf-8-support) that this will change the current default Windows ANSI code page to 65501 (UTF-8), and all the C funtions, except `__argv`, `_acmdln`, and `_pgmptr`, will expect strings in the UTF-8 encoding. Preliminarily testing this statement, I found besides `std::filesystem::path`, functions/classes including `fopen`, `freopen`, `rename`, `std::ofstream`, `std::ifstream` (and `std::cout` but I'm not very sure) all accept UTF-8 strings. It seems that simply say `setlocale(LC_ALL,".UTF-8");` at the beginning of `main()` can make all libraries whose authors did not use wide Win32 API at all, accept UTF-8 strings. Though this feature was introduced in Windows 10 build 17134, it should work on Windows 7/8.1 by statically linking to version 17134 of the Windows SDK or later.  
  
Anyway, I think this statement is worth mentioning in Subsection *Alternatives* of Boost.Nowide's document. (You did mention [this documentation](https://docs.microsoft.com/en-us/windows/apps/design/globalizing/use-utf8-code-page), but this method is slightly more complex and only works on Windows 10 version 1903 or later.)

---

## Comment 3

> Username: u8cat  
> Created at: 2021-08-20 15:31:10 UTC  
> Url: https://github.com/boostorg/nowide/issues/136#issuecomment-902776766  

I realize I have made a very stupid mistake, as I suddenly find that `std::filesystem::path::u8string()` is available in C++ 17.

---

## Comment 4

> Username: Flamefire  
> Created at: 2021-08-20 20:13:07 UTC  
> Url: https://github.com/boostorg/nowide/issues/136#issuecomment-902931749  

I'd still keep this open as I think it makes more sense to allow directly `cout << my_path` rather than `cout << my_path.u8string()`

---

## Comment 5

> Username: Flamefire  
> Created at: 2022-03-07 16:47:26 UTC  
> Url: https://github.com/boostorg/nowide/issues/136#issuecomment-1060898495  

Just a quick info update: This is even harder to handle: `os << path` is actually run as `os << std::quoted(path.string())` to handle roundtrip, i.e. `s << path; ...; s >>path;` Using a path with whitespace otherwise cannot be read, same e.g. for the `u8string()`-approach.  
  
And `path.string()` is defined by the standard to be a possibly lossy conversion, i.e. there may be no way to get the original path back. See https://en.cppreference.com/w/cpp/filesystem/path/string  
  
> if `path::value_type` is `wchar_t`, conversion, if any, is unspecified  
  
I.e. it especially doesn't define any encoding for the resulting `string`.  
  
This also applies to any other (narrow) stream, e.g. as you noted the `fstream`-classes, but also `std::stringstream` and more.     
And there simply is no general solution except using e.g. `u8string` and not relying on whitespace for input parsing.  
  
A possible compromise for Boost.Nowide could be an extra header, e.g. `<boost/nowide/std_filesystem_io.hpp>` which on inclusion enables the `operator<<` and `operator>>` for the nowide stream classes and `std::filesystem::path` with basically the effect of `os << std::quoted(reinterpret_cast<const char*>(path.u8string().c_str()))` (i.e. output the quoted, UTF-8 encoded string, cast required for C++20 and up)  
  
The extra header is to avoid inclusion of `<iomanip>` and `<filesystem>` in the other Nowide headers.  
  
Any comments welcome!

---

## Comment 6

> Username: SandroWissmann  
> Created at: 2022-11-03 19:41:04 UTC  
> Url: https://github.com/boostorg/nowide/issues/136#issuecomment-1302584177  

So if I understand this correctly:  
  
At the moment if I want to use `std::filesystem` with boost::nowide I need to use `std::filesystem::path::u8string()` and then I should be safe right?  
  
I just ask because I only want to use `boost::nowide` with `std::fileystem` and not `boost::filesystem`.  
Will there be other pitfalls with `std::filesystem` ?

---

## Comment 7

> Username: Flamefire  
> Created at: 2022-11-12 12:41:27 UTC  
> Url: https://github.com/boostorg/nowide/issues/136#issuecomment-1312470695  

> At the moment if I want to use std::filesystem with boost::nowide I need to use std::filesystem::path::u8string() and then I should be safe right?  
  
Yes  
  
>  Will there be other pitfalls with std::filesystem ?  
  
Yes: You need to quote the string for reading/writing: `os << std::quoted(reinterpret_cast<const char*>(path.u8string().c_str()))`  
  
So first you (may) need the cast as writing an char8_t C string may not be directly possible and 2nd you need to use `std::quoted` to avoid the parsing of that path to fail when it contains whitespace.

---

## Comment 8

> Username: SandroWissmann  
> Created at: 2022-11-13 19:52:01 UTC  
> Url: https://github.com/boostorg/nowide/issues/136#issuecomment-1312807257  

Thanks for the reply. I will soon port to std::filesystem and use nowide for a application which was originally all done in c. I'm curious if it will then really be painful since it should run on linux and window.

---

## Comment 9

> Username: SandroWissmann  
> Created at: 2023-02-25 12:28:39 UTC  
> Updated at: 2023-02-25 12:31:19 UTC  
> Url: https://github.com/boostorg/nowide/issues/136#issuecomment-1445097845  

I recently tryed to switch to C++20 and now it looks like there is trouble with u8string.  
  
When I know obtain the right path with:  
os << std::quoted(reinterpret_cast<const char*>(path.u8string().c_str()))  
  
This does not seem to work anymore because now u8string().c_str() now returns a char8_t which cannot be converted to a std::string.  
  
  
What do here? Would it be better for now to also use boost::filesystem instead of std::filesystem?  
  
If I understand correctly if I use boost::filesystem and call   
[boost::nowide::nowide_filesystem()](https://www.boost.org/doc/libs/master/libs/nowide/doc/html/namespaceboost_1_1nowide.html#a00544a475ceab4163eb8c12ee80eb9ec)  
  
at the begining all the utf8 stuff should be just handled in the std::strings?

---

## Comment 10

> Username: Flamefire  
> Created at: 2023-02-25 13:47:20 UTC  
> Url: https://github.com/boostorg/nowide/issues/136#issuecomment-1445123987  

> When I know obtain the right path with: os << std::quoted(reinterpret_cast<const char*>(path.u8string().c_str()))  
>   
> This does not seem to work anymore because now u8string().c_str() now returns a char8_t which cannot be converted to a std::string.  
  
I don't understand the issue. Of course `u8string().c_str()` returns a `const char8_t*` out of the `std::basic_string<char8_t>` of `u8string()` in C++20.  
Of course that can not be converted to a `std::string` but why/where would you want to do that?  
  
The mentioned code should be able to output a `std::filesystem::path` on C++17 and C++20 correctly. Check that you are using that and not something else.  
  
I'm still inclined in adding a wrapper for reading and writing a `std::filesystem::path` to Boost.Nowide, just didn't have the time to do that yet. It would likely look like `os << boost::nowide::u8quoted(path)` and wrap the above approach.

---

## Comment 11

> Username: SandroWissmann  
> Created at: 2023-03-02 19:32:53 UTC  
> Url: https://github.com/boostorg/nowide/issues/136#issuecomment-1452442142  

Thanks a lot. I have to admit that I realized for my use case I could also just use Path everywhere and not even convert to string since path can be directly passed to boost::nowide::fstream.   
  
Stiil I will check out what you did when I have the time. could be still helpful

---

## Comment 12

> Username: Flamefire  
> Created at: 2023-03-03 13:45:46 UTC  
> Url: https://github.com/boostorg/nowide/issues/136#issuecomment-1453555239  

> Thanks a lot. I have to admit that I realized for my use case I could also just use Path everywhere and not even convert to string since path can be directly passed to boost::nowide::fstream.  
  
Yes that's the idea: Never having to convert a path to a string which may be lossy.  
  
But e.g. for outputting it to users or a file it may be necessary which is where the new manipulator comes in.

---

## Comment 13

> Username: andim2  
> Created at: 2025-05-26 06:11:20 UTC  
> Url: https://github.com/boostorg/nowide/issues/136#issuecomment-2908648575  

This issue discussion hasn't mentioned `std::filesystem` `u8path()` keyword yet, yet _AFAICS_ this is the _symmetric_ counterpart to `u8string()` (i.e. these two are the relevant *) _boundary transition_ activities (of raw `string`, _UTF-8_ encoded) to/fro first-class-citizen **) `filesystem` `path`-typed objects).  
_IOW_, _AFAICS_ `path`-typed objects _always_ have to be constructed via `u8path()` (or _wide_-typed input), else they _won't_ have properly fully _Unicode_-compliant state (/ behaviour).  
*) and potentially very lossy (**DATA LOSS CORRUPTION**), whenever that transition is done incorrectly (i.e. NOT preserving _Unicode_ protocol compliance [requirements])...  
**) _high-level_ / _domain-specific_  
  
Side note:  
[`u8path()`](https://en.cppreference.com/w/cpp/filesystem/path/u8path) is deprecated in _C++20_, likely due to `u8string` type - which probably provides for some weird asymmetric handling experience in various standards/environments which do [not] choose `std::u8string` ...  
- [Is it bad to always use filesystem::path instead of std::string?](https://www.reddit.com/r/cpp_questions/comments/18wkzjr/comment/kfyhcuh/)  
  
===> yay, more incompatibility quirks ===> abstracting wrappers needed?

---

## Comment 14

> Username: Flamefire  
> Created at: 2025-05-26 07:08:47 UTC  
> Url: https://github.com/boostorg/nowide/issues/136#issuecomment-2908783623  

`boost::filesystem::path` contrary to `std` provides an `imbue` to make all conversions from narrow strings UTF-8 as well as supporting wide strings so there is less need for `u8path`. So for that you can just use `std::string`  
  
> or wide-typed input), else they won't have properly fully Unicode-compliant state (/ behaviour).  
Actually not even that: If the narrow string isn't valid UTF-8 or the wide string contains characters that can't be converted to UTF-8 you still loose data.  
  
In other projects I started converting every input (user input, paths....) to UTF-8 with an error if it was invalid (or ignored if useful) and use `std::string` with UTF-8 encoded chars. Was a good trade-off. `char8_t` is nice to "enforce" this via the type system, but has its own drawbacks.  
  
Anyway I think this discussion strays away from what this issue was about: `bnw::cout << bnw::quoted(path)` works, at least where `u8string` would give correct results.

---

## Comment 15

> Username: andim2  
> Created at: 2025-05-26 09:06:49 UTC  
> Updated at: 2025-05-26 09:07:41 UTC  
> Url: https://github.com/boostorg/nowide/issues/136#issuecomment-2909020551  

> `boost::filesystem::path` contrary to `std` provides an `imbue` to make all conversions from narrow strings UTF-8 as well as supporting wide strings so there is less need for `u8path`. So for that you can just use `std::string`  
  
Right, indeed... _if_ an environment has that `imbue `set to _proper_ config state (else pain - thus maybe some local wrapper helpers needed).  
  
> > or wide-typed input), else they won't have properly fully Unicode-compliant state (/ behaviour).  
> > Actually not even that: If the narrow string isn't valid UTF-8 or the wide string contains characters that can't be converted to UTF-8 you still loose data.  
  
Uh yeah. Which probably means that...  
  
> In other projects I started converting every input (user input, paths....) to UTF-8 with an error if it was invalid (or ignored if useful) and use `std::string` with UTF-8 encoded chars. Was a good trade-off. `char8_t` is nice to "enforce" this via the type system, but has its own drawbacks.  
  
...having such boundary checking added at central hotpath locations is  
_especially important_ given this _general_ encoding mess situation - e.g. as described at  
[Also use UTF-8 in wxString::wxString on Windows](https://github.com/wxWidgets/wxWidgets/issues/18113#issuecomment-1011182211)  
  
> Anyway I think this discussion strays away from what this issue was about: `bnw::cout << bnw::quoted(path)` works, at least where `u8string` would give correct results.  
  
Yeah, sorry, maybe I should not have hijacked this issue ;-P  
  
Thanks for very insightful additions!
