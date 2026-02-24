# #181 - fstream broken on mingw64 [Closed]

> Username: rainerdeyke  
> Created at: 2021-03-23 07:37:12 UTC  
> Updated at: 2025-08-11 02:36:20 UTC  
> Closed at: 2021-06-06 21:17:06 UTC  
> Url: https://github.com/boostorg/filesystem/issues/181  

I noticed that boost::filesystem::fstream does not properly handle unicode on mingw64.  Here is the offending code:  
  
`//  on Windows, except for standard libaries known to have wchar_t overloads for`   
`//  file stream I/O, use path::string() to get a narrow character c_str()`  
`#if defined(BOOST_WINDOWS_API) \`  
`  && (!defined(_CPPLIB_VER) || _CPPLIB_VER < 405 || defined(_STLPORT_VERSION))`  
`  // !Dinkumware || early Dinkumware || STLPort masquerading as Dinkumware`  
`# define BOOST_FILESYSTEM_C_STR string().c_str()  // use narrow, since wide not available`  
`#else  // use the native c_str, which will be narrow on POSIX, wide on Windows`  
`# define BOOST_FILESYSTEM_C_STR c_str()`  
`#endif`  
  
In other words, boost::filesystem::fstream unnecessarily, without warning, converts my unicode strings to narrow strings.  (I am compiling in C++17 mode, so proper wide character support part of the standard where std::filesystem::path::value_type == wchar_t.)  
  
Correct behavior would be to always use wide characters on Windows, and to fail with a compile time error if this is not possible.  A compile-time error is always better than silent incorrect behavior.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-03-23 08:35:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/181#issuecomment-804719217  

The comment clearly states that some standard libraries don't support `wchar_t` paths. I don't know if libstdc++ from MinGW-w64 is actually one of them since the comment probably predates it. If it does support it then I need a way to detect its version where the support was introduced.  
  
> Correct behavior would be to always use wide characters on Windows, and to fail with a compile time error if this is not possible.  
  
I disagree, as this would render Boost.Filesystem unusable, even with ASCII-only paths, which is a significant amount of use cases.

---

## Comment 2

> Username: rainerdeyke  
> Created at: 2021-03-23 10:04:25 UTC  
> Url: https://github.com/boostorg/filesystem/issues/181#issuecomment-804775622  

> The comment clearly states that some standard libraries don't support `wchar_t` paths. I don't know if libstdc++ from MinGW-w64 is actually one of them since the comment probably predates it. If it does support it then I need a way to detect its version where the support was introduced.  
  
I am currently using this workaround:  
`std::ofstream f(boost_path.c_str());`  
So, yes, mingw-w64 supports wchar_t paths, at least when compiling with --std=c++17.  
  
> I disagree, as this would render Boost.Filesystem unusable, even with ASCII-only paths, which is a significant amount of use cases.  
  
It would only break boost::filesystem::fstream, as the rest of Boost.Filesystem correctly handles unicode filenames.  The whole point of boost::filesystem::fstream is to correctly handle unicode filenames.  If somebody is OK with only ASCII paths, they can just use std::fstream directly.  
  
I disagree that there are a significant amount of use cases for ASCII-only paths.  Instead, there are a significant amount of use cases where the programmer may think that they are only dealing with ASCII paths but which will break when on a computer with non-ASCII paths.  In order to stay ASCII-only, a program has to avoid boost::filesystem::absolute, boost::filesystem::initial_path, boost::filesystem::current_path, boost::filesystem::temp_directory_path, and paths from user input.  That's a lot harder than just avoiding boost::filesystem::fstream.

---

## Comment 3

> Username: Lastique  
> Created at: 2021-03-23 10:45:21 UTC  
> Url: https://github.com/boostorg/filesystem/issues/181#issuecomment-804801037  

> So, yes, mingw-w64 supports wchar_t paths, at least when compiling with --std=c++17.  
  
Ok, good, but I need a version.  
  
>  The whole point of boost::filesystem::fstream is to correctly handle unicode filenames.  
  
No, it is a way to portably integrate `boost::filesystem::path` with `std::fstream`. When possible, this includes directly passing `wchar_t` paths to the underlying API. When not, character code conversion is performed, which should still work reasonably well, including with non-ASCII characters, as long as the locale is configured properly. At the very least, ASCII-only paths should always work, and this is a large amount of use cases. In other words, `boost::filesystem::fstream` exists to be able to construct it from `boost::filesystem::path` on any platform with any standard library and with any native path character type, so that it works in the best possible way on that combination.  
  
People are free to use or not use `boost::filesystem::fstream`. If you consider it unacceptable then you can always use `std::fstream`, there's nothing wrong with that.

---

## Comment 4

> Username: rainerdeyke  
> Created at: 2021-03-23 11:24:42 UTC  
> Url: https://github.com/boostorg/filesystem/issues/181#issuecomment-804824695  

> > So, yes, mingw-w64 supports wchar_t paths, at least when compiling with --std=c++17.  
>   
> Ok, good, but I need a version.  
  
Don't check for a specific compiler or version.  Check for std::is_constructible<std::fstream, wchar_t *>.

---

## Comment 5

> Username: Lastique  
> Created at: 2021-03-23 11:46:04 UTC  
> Url: https://github.com/boostorg/filesystem/issues/181#issuecomment-804836580  

`std::is_constructible` is C++11, and `boost::is_constructible` falls back to `boost::is_convertible`, which will not work since the `std::fstream` constructors from path are `explicit`. And I'd prefer a preprocessor check anyway.

---

## Comment 6

> Username: rainerdeyke  
> Created at: 2021-03-23 13:28:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/181#issuecomment-804903166  

So check for __cplusplus >= 201103L, and then check for std::is_constructible.  
  
You could probably skip the std::is_constructible check if __cplusplus >= 201703L, because std::fstream::fstream(std::filesystem::path::value_type *) is mandated by the C++17 standard, and as far as I know every Windows implementation of std::filesystem uses wchar_t as value_type.  You could also fall back to boost::nowide::fstream (if __cplusplus >= 201103L) instead of using std::fstream as back end.

---

## Comment 7

> Username: Lastique  
> Created at: 2021-06-09 15:32:51 UTC  
> Updated at: 2021-06-09 15:33:13 UTC  
> Url: https://github.com/boostorg/filesystem/issues/181#issuecomment-857805973  

FYI, I had to partially revert the commit since using `std::filesystem` doesn't work on Windows in MinGW-w64 and Cygwin. Also, libstdc++ does not provide file stream constructors from `const wchar_t*` paths, so this bug report was invalid to begin with.

---

## Comment 8

> Username: edo9300  
> Created at: 2025-08-10 23:32:58 UTC  
> Url: https://github.com/boostorg/filesystem/issues/181#issuecomment-3172967040  

> FYI, I had to partially revert the commit since using `std::filesystem` doesn't work on Windows in MinGW-w64 and Cygwin. Also, libstdc++ does not provide file stream constructors from `const wchar_t*` paths, so this bug report was invalid to begin with.  
  
Sorry for the necrobump, but i don't get where this conclusion came from, gcc (and by extension libstdc++) have supported using wchars to open fstreams since gcc 10, more specifically this commit https://github.com/gcc-mirror/gcc/commit/b02923592a55b52d796656c5b4f34a964a2604a9, and even if maybe at the time of this issue (2021) the support wasn't fully there, in 2025 std::filesystem is properly supported on mingw with wide chars, so it's weird that boost filesystem doesn't.

---

## Comment 9

> Username: Lastique  
> Created at: 2025-08-11 02:35:39 UTC  
> Updated at: 2025-08-11 02:36:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/181#issuecomment-3173120763  

> Sorry for the necrobump, but i don't get where this conclusion came from  
  
It came from experience at the time. You can see some evidence in the referenced commit https://github.com/boostorg/filesystem/commit/29ef7d683daeff81713eebb3dc17cb10e2dfc233. In particular, no, gcc 10 doesn't work. Granted, I did not revisit this since back then, so maybe things have improved.
