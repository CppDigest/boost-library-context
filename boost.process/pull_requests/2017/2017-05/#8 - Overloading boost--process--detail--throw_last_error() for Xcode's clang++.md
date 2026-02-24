# #8 Overloading boost::process::detail::throw_last_error() for Xcode's clang++ [Merged]

> Username: tabe  
> Created at: 2017-05-01 08:51:23 UTC  
> Updated at: 2017-05-01 08:54:33 UTC  
> Merged at: 2017-05-01 08:54:33 UTC  
> Closed at: 2017-05-01 08:54:33 UTC  
> Url: https://github.com/boostorg/process/pull/8  

In order to build boost::process::child() with Xcode, this change provides an alternative overloading of boost::process::detail::throw_last_error().  
  
For example I got the following compilation error against Boost 1.64.0 with Xcode 5.1.1, 6.2, 7.2.1, and 8.3.2 consistently (under -std=c++11):  
> In file included from ../../../src/gui/view-frame.cc:6:  
> In file included from ../../../src/gui/view-frame.h:8:  
> In file included from /Users/abe/src/Flint/xcode/usr/include/boost/process/child.hpp:21:  
> In file included from /Users/abe/src/Flint/xcode/usr/include/boost/process/detail/child_decl.hpp:29:  
> /Users/abe/src/Flint/xcode/usr/include/boost/process/detail/posix/terminate.hpp:26:9: error: no matching function for call to 'throw_last_error'  
>         boost::process::detail::throw_last_error("kill(2) failed");  
>         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
> /Users/abe/src/Flint/xcode/usr/include/boost/process/detail/config.hpp:70:13: note: candidate function not viable: no known conversion from 'const char [15]' to 'const std::string' (aka 'const basic_string<char, char_traits<char>, allocator<char> >') for 1st argument  
> inline void throw_last_error(const std::string & msg)  
>             ^  
> /Users/abe/src/Flint/xcode/usr/include/boost/process/detail/config.hpp:75:13: note: candidate function not viable: requires 0 arguments, but 1 was provided  
> inline void throw_last_error()  
>             ^  
>

---
