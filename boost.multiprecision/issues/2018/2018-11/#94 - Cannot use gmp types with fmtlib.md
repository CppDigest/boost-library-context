# #94 - Cannot use gmp types with fmtlib [Closed]

> Username: vbeffara  
> Created at: 2018-11-03 11:25:37 UTC  
> Updated at: 2019-04-15 20:54:29 UTC  
> Closed at: 2018-11-03 13:06:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/94  

Boost version 1.68.0, fmtlib version 5.2.1, clang 7 (from homebrew) on macOS Mojave.  
  
Trying to use mpz_int with fmtlib (for logging with spdlog), I land on the following error. Here is the code:  
  
```c++  
#include <boost/multiprecision/gmp.hpp>  
#include <fmt/format.h>  
  
int main(int argc, char **argv) {  
  fmt::format("{}", boost::multiprecision::mpz_int{2});  
}  
```  
  
And here is the error message:  
  
```  
In file included from bla.cpp:1:  
In file included from /usr/local/include/boost/multiprecision/gmp.hpp:9:  
In file included from /usr/local/include/boost/multiprecision/number.hpp:22:  
In file included from /usr/local/include/boost/multiprecision/detail/generic_interconvert.hpp:9:  
/usr/local/include/boost/multiprecision/detail/default_ops.hpp:926:93: error: cannot convert 'std::__1::numeric_limits<fmt::v5::basic_string_view<char> >::type' (aka 'fmt::v5::basic_string_view<char>') to 'next_type' (aka 'double') without a conversion operator  
   if(std::numeric_limits<R>::is_specialized && std::numeric_limits<R>::is_bounded && ((n > (next_type)(std::numeric_limits<R>::max)() || (n < (next_type)-(std::numeric_limits<R>::max)()) )))  
                                                                                            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/multiprecision/number.hpp:590:7: note: in instantiation of function template specialization 'boost::multiprecision::default_ops::eval_convert_to<fmt::v5::basic_string_view<char>, boost::multiprecision::backends::gmp_int>' requested here  
      eval_convert_to(result, m_backend);  
      ^  
/usr/local/include/boost/multiprecision/number.hpp:606:7: note: in instantiation of function template specialization 'boost::multiprecision::number<boost::multiprecision::backends::gmp_int, boost::multiprecision::et_on>::convert_to_imp<fmt::v5::basic_string_view<char> >' requested here  
      convert_to_imp(&result);  
      ^  
/usr/local/include/boost/multiprecision/number.hpp:628:29: note: in instantiation of function template specialization 'boost::multiprecision::number<boost::multiprecision::backends::gmp_int, boost::multiprecision::et_on>::convert_to<fmt::v5::basic_string_view<char> >' requested here  
      return this->template convert_to<T>();  
                            ^  
fmt/include/fmt/core.h:714:61: note: in instantiation of function template specialization 'boost::multiprecision::number<boost::multiprecision::backends::gmp_int, boost::multiprecision::et_on>::operator basic_string_view<fmt::v5::basic_string_view<char>, 0>' requested here  
  make_value(const T &val) { return basic_string_view<Char>(val); }  
                                                            ^  
fmt/include/fmt/core.h:1022:10: note: in instantiation of function template specialization 'fmt::v5::internal::make_value<fmt::v5::basic_format_context<std::__1::back_insert_iterator<fmt::v5::internal::basic_buffer<char> >, char>, boost::multiprecision::number<boost::multiprecision::backends::gmp_int, boost::multiprecision::et_on>, char>' requested here  
  return make_value<Context>(value);  
         ^  
fmt/include/fmt/core.h:1132:23: note: in instantiation of function template specialization 'fmt::v5::internal::make_arg<true, fmt::v5::basic_format_context<std::__1::back_insert_iterator<fmt::v5::internal::basic_buffer<char> >, char>, boost::multiprecision::number<boost::multiprecision::backends::gmp_int, boost::multiprecision::et_on> >' requested here  
    : data_{internal::make_arg<IS_PACKED, Context>(args)...} {}  
                      ^  
fmt/include/fmt/core.h:1327:5: note: in instantiation of member function 'fmt::v5::format_arg_store<fmt::v5::basic_format_context<std::__1::back_insert_iterator<fmt::v5::internal::basic_buffer<char> >, char>, boost::multiprecision::number<boost::multiprecision::backends::gmp_int, boost::multiprecision::et_on> >::format_arg_store' requested here  
    format_arg_store<context, Args...>(args...) {  
    ^  
fmt/include/fmt/core.h:1450:6: note: in instantiation of member function 'fmt::v5::internal::checked_args<char [3], boost::multiprecision::number<boost::multiprecision::backends::gmp_int, boost::multiprecision::et_on> >::checked_args' requested here  
    *internal::checked_args<S, Args...>(format_str, args...));  
     ^  
bla.cpp:5:8: note: in instantiation of function template specialization 'fmt::v5::format<char [3], boost::multiprecision::number<boost::multiprecision::backends::gmp_int, boost::multiprecision::et_on> >' requested here  
  fmt::format("{}", boost::multiprecision::mpz_int{2});  
       ^  
1 error generated.  
```  
  
The issue first appeared when fmtlib introduced an optimization where for types that could be converted to a string_view, it would use that rather than the insertion operator into a stream for formatting. As far as I understand, boost::multiprecision::number declares an overly eager conversion operator (claiming to be convertible to anything? number.hpp:912) and then fmtlib tries to use that, and it fails because only conversions to numerical types are available. The fix seems to be to restrict the conversion operator to arithmetic types.  
  
There is something surrounding this line involving workarounds and is_arithmetic so the fix might well be implemented already?  
  
Corresponding issue at fmtlib: https://github.com/fmtlib/fmt/issues/918

---

## Comment 1

> Username: vbeffara  
> Created at: 2018-11-03 11:33:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/94#issuecomment-435580885  

Probably related to https://github.com/boostorg/multiprecision/commit/06d03409c0488d7982174a3e3b59097b03d60c9e  
  
I lack the time to set up a test against master to see if the issue persists though.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-11-03 13:06:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/94#issuecomment-435586579  

I've tested current master against fmtlib master with gcc-8.1 and -std=c++17 and everything seems OK.  
  
Closing as fixed, but please reopen if the next release doesn't address this.  
  
BTW I needed to add  
  
```  
#include <fmt/ostream.h>  
```  
  
to your test case to get things to build.

---

## Comment 3

> Username: vbeffara  
> Created at: 2018-12-19 08:13:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/94#issuecomment-448506335  

AFAICT the fix from master has not been included in boost 1.69 (same code and config as above otherwise):  
  
```  
In file included from gmp_fmt.cpp:1:  
In file included from /Users/vincent/.conan/data/boost/1.69.0/conan/stable/package/565568150d9c463ce960e1eb3fd583717906e1cd/include/boost/multiprecision/gmp.hpp:9:  
In file included from /Users/vincent/.conan/data/boost/1.69.0/conan/stable/package/565568150d9c463ce960e1eb3fd583717906e1cd/include/boost/multiprecision/number.hpp:24:  
In file included from /Users/vincent/.conan/data/boost/1.69.0/conan/stable/package/565568150d9c463ce960e1eb3fd583717906e1cd/include/boost/multiprecision/detail/generic_interconvert.hpp:9:  
/Users/vincent/.conan/data/boost/1.69.0/conan/stable/package/565568150d9c463ce960e1eb3fd583717906e1cd/include/boost/multiprecision/detail/default_ops.hpp:956:93: error: cannot convert  
      'std::__1::numeric_limits<fmt::v5::basic_string_view<char> >::type' (aka 'fmt::v5::basic_string_view<char>') to 'next_type' (aka 'double') without a conversion operator  
   if(std::numeric_limits<R>::is_specialized && std::numeric_limits<R>::is_bounded && ((n > (next_type)(std::numeric_limits<R>::max)() || (n < (next_type)-(std::numeric_limits<R>::max)()) )))  
                                                                                            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/Users/vincent/.conan/data/boost/1.69.0/conan/stable/package/565568150d9c463ce960e1eb3fd583717906e1cd/include/boost/multiprecision/number.hpp:866:7: note: in instantiation of function template  
      specialization 'boost::multiprecision::default_ops::eval_convert_to<fmt::v5::basic_string_view<char>, boost::multiprecision::backends::gmp_int>' requested here  
      eval_convert_to(result, m_backend);  
      ^  
/Users/vincent/.conan/data/boost/1.69.0/conan/stable/package/565568150d9c463ce960e1eb3fd583717906e1cd/include/boost/multiprecision/number.hpp:882:7: note: in instantiation of function template  
      specialization 'boost::multiprecision::number<boost::multiprecision::backends::gmp_int, boost::multiprecision::et_on>::convert_to_imp<fmt::v5::basic_string_view<char> >' requested here  
      convert_to_imp(&result);  
      ^  
/Users/vincent/.conan/data/boost/1.69.0/conan/stable/package/565568150d9c463ce960e1eb3fd583717906e1cd/include/boost/multiprecision/number.hpp:914:29: note: in instantiation of function template  
      specialization 'boost::multiprecision::number<boost::multiprecision::backends::gmp_int, boost::multiprecision::et_on>::convert_to<fmt::v5::basic_string_view<char> >' requested here  
      return this->template convert_to<T>();  
                            ^  
/Users/vincent/.conan/data/fmt/5.2.1/bincrafters/stable/package/9d9605a01b2be03d22245053ed52dbb6178f7b92/include/fmt/core.h:700:61: note: in instantiation of function template specialization  
      'boost::multiprecision::number<boost::multiprecision::backends::gmp_int, boost::multiprecision::et_on>::operator basic_string_view<fmt::v5::basic_string_view<char> >' requested here  
  make_value(const T &val) { return basic_string_view<Char>(val); }  
                                                            ^  
/Users/vincent/.conan/data/fmt/5.2.1/bincrafters/stable/package/9d9605a01b2be03d22245053ed52dbb6178f7b92/include/fmt/core.h:1056:10: note: in instantiation of function template specialization  
      'fmt::v5::internal::make_value<fmt::v5::basic_format_context<std::__1::back_insert_iterator<fmt::v5::internal::basic_buffer<char> >, char>,  
      boost::multiprecision::number<boost::multiprecision::backends::gmp_int, boost::multiprecision::et_on>, char>' requested here  
  return make_value<Context>(value);  
         ^  
/Users/vincent/.conan/data/fmt/5.2.1/bincrafters/stable/package/9d9605a01b2be03d22245053ed52dbb6178f7b92/include/fmt/core.h:1114:23: note: in instantiation of function template specialization  
      'fmt::v5::internal::make_arg<true, fmt::v5::basic_format_context<std::__1::back_insert_iterator<fmt::v5::internal::basic_buffer<char> >, char>,  
      boost::multiprecision::number<boost::multiprecision::backends::gmp_int, boost::multiprecision::et_on> >' requested here  
    : data_{internal::make_arg<IS_PACKED, Context>(args)...} {}  
                      ^  
/Users/vincent/.conan/data/fmt/5.2.1/bincrafters/stable/package/9d9605a01b2be03d22245053ed52dbb6178f7b92/include/fmt/core.h:1444:40: note: in instantiation of member function  
      'fmt::v5::format_arg_store<fmt::v5::basic_format_context<std::__1::back_insert_iterator<fmt::v5::internal::basic_buffer<char> >, char>,  
      boost::multiprecision::number<boost::multiprecision::backends::gmp_int, boost::multiprecision::et_on> >::format_arg_store' requested here  
  format_arg_store<context_t, Args...> as{args...};  
                                       ^  
gmp_fmt.cpp:6:8: note: in instantiation of function template specialization 'fmt::v5::format<char [3], boost::multiprecision::number<boost::multiprecision::backends::gmp_int, boost::multiprecision::et_on>  
      >' requested here  
  fmt::format("{}", boost::multiprecision::mpz_int{2});  
       ^  
1 error generated.  
```

---

## Comment 4

> Username: vbeffara  
> Created at: 2018-12-19 08:38:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/94#issuecomment-448512949  

P.S., I cannot reopen the issue myself, and don't want to create a new one just for the update.

---

## Comment 5

> Username: vbeffara  
> Created at: 2019-04-14 10:20:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/94#issuecomment-482942007  

Still not fixed in 1.70 ... for the moment a fix is to define this:  
```c++  
template <typename T> struct std::is_constructible<fmt::string_view, boost::multiprecision::number<T>> : std::false_type {};  
```

---

## Comment 6

> Username: pabristow  
> Created at: 2019-04-14 10:45:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/94#issuecomment-482946517  

Is this related to this puzzling message on a simple showing a numeric_limits value for a cpp_bin_*  
  
which runs OK using gcc810 but fails for Clang 800?  
  
I've added #include <string_view> but that doesn't help, perhaps because the reported compiler version is not enabling the string view stuff?  
  
-------------- Build: clang800_Debug in show_numeric_limits (compiler: LLVM Clang 800 Compiler)---------------  
  
clang-cl.exe -Wall -fexceptions -w -g -Wno-reserved-id-macro -Wno-unknown-argument -fpermissive -Wno-c++98-compat-pedantic -Wno-c++98-compat -Wno-pedantic -std=c++17 -I..\..\..\boost -c I:\boost\libs\hello_boost\example\show_numeric_limits\show_numeric_limits.cpp -o obj\Debug\boost\libs\hello_boost\example\show_numeric_limits\show_numeric_limits.o  
clang-cl.exe  -o bin\Debug\show_numeric_limits.exe obj\Debug\boost\libs\hello_boost\example\show_numeric_limits\show_numeric_limits.o     
I:\boost\libs\hello_boost\example\show_numeric_limits\show_numeric_limits.cpp(20,12): error: no member named 'basic_string_view' in namespace 'std'  
using std::basic_string_view;  
      ~~~~~^  
In file included from I:\boost\libs\hello_boost\example\show_numeric_limits\show_numeric_limits.cpp:45:  
In file included from ..\..\..\boost\boost/multiprecision/cpp_bin_float.hpp:9:  
In file included from ..\..\..\boost\boost/multiprecision/cpp_int.hpp:12:  
In file included from ..\..\..\boost\boost/multiprecision/number.hpp:24:  
In file included from ..\..\..\boost\boost/multiprecision/detail/generic_interconvert.hpp:9:  
..\..\..\boost\boost/multiprecision/detail/default_ops.hpp(852,64): error: no template named 'basic_string_view' in namespace 'std'  
inline void assign_from_string_view(Result& result, const std::basic_string_view<char, Traits>& view)  
                                                          ~~~~~^  
..\..\..\boost\boost/multiprecision/detail/default_ops.hpp(860,64): error: no template named 'basic_string_view' in namespace 'std'  
inline void assign_from_string_view(Result& result, const std::basic_string_view<char, Traits>& view_x, const std::basic_string_view<char, Traits>& view_y)  
                                                          ~~~~~^  
..\..\..\boost\boost/multiprecision/detail/default_ops.hpp(860,116): error: no template named 'basic_string_view' in namespace 'std'  
inline void assign_from_string_view(Result& result, const std::basic_string_view<char, Traits>& view_x, const std::basic_string_view<char, Traits>& view_y)

---

## Comment 7

> Username: jzmaddock  
> Created at: 2019-04-14 12:38:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/94#issuecomment-482966191  

This works for me, I have as my test case:  
  
```  
#include <boost/version.hpp>  
#include <boost/multiprecision/gmp.hpp>  
#include <fmt/format.h>  
#include <fmt/ostream.h>  
  
#if BOOST_VERSION < 107000  
#error "Incorrect Boost version"  
#endif  
  
int main(int argc, char **argv) {  
  fmt::format("{}", boost::multiprecision::mpz_int{2});  
}  
```  
And   
```  
gcc version 7.3.0 (Ubuntu 7.3.0-27ubuntu1~18.04)   
```  
Plus current fmtlib master, and Boost-1.70, and it all builds just fine.  
  
I suspect the OP has the wrong Boost version in their #include path?

---

## Comment 8

> Username: vbeffara  
> Created at: 2019-04-14 21:39:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/94#issuecomment-483060542  

I do have boost 1.70 in my path, and still get the same error message:  
  
```  
/Users/vincent/.conan/data/boost/1.70.0/conan/stable/package/0a167935b5fb9fa4d45e288ae29a76df3384260c/include/boost/multiprecision/detail/default_ops.hpp:956:93: error: cannot convert 'std::__1::numeric_limits<fmt::v5::basic_string_view<char> >::type' (aka 'fmt::v5::basic_string_view<char>') to 'next_type' (aka 'double') without a conversion operator  
   if(std::numeric_limits<R>::is_specialized && std::numeric_limits<R>::is_bounded && ((n > (next_type)(std::numeric_limits<R>::max)() || (n < (next_type)-(std::numeric_limits<R>::max)()) )))  
                                                                                            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
That is using boost 1.70.0, fmt 5.3.0, and clang 8.0.0 (all from homebrew on mac os X 10.14.4). It does compile fine with gcc-8 on the same machine though. Could the issue be with clang then?

---

## Comment 9

> Username: vbeffara  
> Created at: 2019-04-15 08:47:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/94#issuecomment-483163951  

Weird, it seems that std::numeric_limits<fmt::basic_string_view<char>>::is_specialized is set to true using clang/libc++ and false using gcc/libstdc++ ? If so, the issue would definitely not be in boost::multiprecision anymore, need to investigate a bit at some point.

---

## Comment 10

> Username: pabristow  
> Created at: 2019-04-15 14:04:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/94#issuecomment-483265342  

For me, the <string_view> comes from Mingw built using gcc 8.1  
  
C:\Program Files\mingw-w64\x86_64-8.1.0-win32-seh-rt_v6-rev0\mingw64\lib\gcc\x86_64-w64-mingw32\8.1.0\include\c++\string_view  
  
I note that this Mingw64 on Windows 10 using CodeBlocks IDE calling clang-cl.exe provides  
  
 __cpluplus = 2011402L   
  
Platform: Win32  
Compiler: Clang version 8.0.0 (tags/RELEASE_800/final)  
STL     : Dinkumware standard library version 650  
Boost   : 1.70.0  
_MSC_VER = 1920, _MSC_BUILD = 1  
_MSC_FULL_VER = 192027508  
Processor type:  x64  
clang 8.0.0__cplusplus  201402.  
  
but the string_view files  
  
#if __cplusplus >= 201703L  
  
// definitions of basic_string_view  
  
but the comment at the end says  
  
#endif // __cplusplus <= 201402L  
  
So I naively patched a copy to 201403L but that still didn't work, perhaps for the reasons you suggest.  
  
So I'm thoroughly confused.

---

## Comment 11

> Username: jzmaddock  
> Created at: 2019-04-15 17:03:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/94#issuecomment-483336103  

>......\boost\boost/multiprecision/detail/default_ops.hpp(852,64): error: no template named 'basic_string_view' in namespace 'std'  
  
Sorry I didn't spot this before: there was a bug in Boost.Config (fixed in develop - but didn't make it in time for Boost-1.70 - see https://github.com/boostorg/config/commit/e27ecad25e909c6e5d699e86c239fd1cddb7e3d9) that enabled std::string_view for clang in non-C++17 mode.  
  
The issue is that `__has_include(<string_view>)` is true, but then actually including it is an error unless you build with -std=c++17.  
  
Anyhow, please update to Config to latest develop or else build with -std=c++17 when using clang/libstdc++.

---

## Comment 12

> Username: jzmaddock  
> Created at: 2019-04-15 17:09:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/94#issuecomment-483338169  

>Weird, it seems that std::numeric_limits<fmt::basic_string_view>::is_specialized is set to true using clang/libc++ and false using gcc/libstdc++ ? If so, the issue would definitely not be in boost::multiprecision anymore, need to investigate a bit at some point.  
  
I missed that you were using clang - however clang/libstdc++ works OK for me.

---

## Comment 13

> Username: pabristow  
> Created at: 2019-04-15 17:15:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/94#issuecomment-483340250  

I am still confused - I am using a fairly recent develop and also using -std=c++17.    
  
Simple tests just constructing a std::string_view good("a literal string.");   
  
pass using b2 and clang-win-8.0.0  
but fails to find string_view using CodeBlocks for reasons I have yet to discover, but would appear to be some in the setting for the IDE.  
  
If I am using clang-win, Am I right in thinking that I am not using libstd++ not libc++ but am using MS/dinkumware?  
  
I will investigate further in a several days time.

---

## Comment 14

> Username: vbeffara  
> Created at: 2019-04-15 20:54:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/94#issuecomment-483414713  

Still digging on this one, I arrive at the following observation: with this code,  
  
```c++  
#include <boost/multiprecision/gmp.hpp>  
#include <iostream>  
  
int main() {  
  std::cerr << std::is_constructible<std::basic_string_view<char>,  
                                     boost::multiprecision::mpz_int>::value  
            << '\n';  
}  
```  
  
with gcc-8/libstdc++ it says 0, with clang-8/libc++ it says 1 (both on OSX).
