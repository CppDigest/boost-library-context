# #9 - compiler errors with gcc-7 and clang-4 [Closed]

> Username: octopus-prime  
> Created at: 2017-08-16 21:36:23 UTC  
> Updated at: 2017-08-23 18:50:08 UTC  
> Closed at: 2017-08-23 18:48:58 UTC  
> Url: https://github.com/boostorg/pfr/issues/9  

Tried to build the "Motivating Example"s.  
  
C++14 example was okay with gcc-6.  
C++14 example and C++17 example did not compile with gcc-7 and clang-4.  
  
Note:  
gcc (Ubuntu 7-20170407-0ubuntu2) 7.0.1 20170407 (experimental) [trunk revision 246759]  
clang version 4.0.0-1ubuntu1 (tags/RELEASE_400/rc1)  
  
C++14 example compiler errors (gcc 7):  
  
```  
gcc.compile.c++ bin/gcc-7/release/source/test_magic.o  
In file included from /home/mike/workspace/magic_get/include/boost/pfr/detail/core17.hpp:11:0,  
                 from /home/mike/workspace/magic_get/include/boost/pfr/precise/core.hpp:20,  
                 from /home/mike/workspace/magic_get/include/boost/pfr/precise.hpp:12,  
                 from /home/mike/workspace/magic_get/include/boost/pfr.hpp:12,  
                 from source/test_magic.cpp:9:  
/home/mike/workspace/magic_get/include/boost/pfr/detail/core17_generated.hpp: In instantiation of ‘constexpr auto boost::pfr::detail::as_tuple_impl(T&&, boost::pfr::detail::size_t_<3>) [with T = const my_struct&; boost::pfr::detail::size_t_<3> = std::integral_constant<long unsigned int, 3>]’:  
/home/mike/workspace/magic_get/include/boost/pfr/detail/core17_generated.hpp:1032:43:   required from ‘constexpr auto boost::pfr::detail::as_tuple(const T&) [with T = my_struct]’  
/home/mike/workspace/magic_get/include/boost/pfr/precise/io.hpp:45:69:   required from ‘void boost::pfr::write(std::basic_ostream<_CharT, _Traits>&, const T&) [with Char = char; Traits = std::char_traits<char>; T = my_struct]’  
/home/mike/workspace/magic_get/include/boost/pfr/precise/ops.hpp:129:26:   required from ‘boost::pfr::detail::enable_not_ostreamable_t<std::basic_ostream<_CharT, _Traits>, T> boost::pfr::ops::operator<<(std::basic_ostream<_CharT, _Traits>&, const T&) [with Char = char; Traits = std::char_traits<char>; T = my_struct; boost::pfr::detail::enable_not_ostreamable_t<std::basic_ostream<_CharT, _Traits>, T> = std::basic_ostream<char>&]’  
source/test_magic.cpp:22:27:   required from here  
/home/mike/workspace/magic_get/include/boost/pfr/detail/core17_generated.hpp:57:9: error: ‘std::tuple_size<const my_struct>::value’ is not an integral constant expression  
   auto& [a,b,c] = std::forward<T>(val);  
         ^~~~~~~  
In file included from /home/mike/workspace/magic_get/include/boost/pfr/precise/ops.hpp:15:0,  
                 from /home/mike/workspace/magic_get/include/boost/pfr/precise.hpp:14,  
                 from /home/mike/workspace/magic_get/include/boost/pfr.hpp:12,  
                 from source/test_magic.cpp:9:  
/home/mike/workspace/magic_get/include/boost/pfr/precise/io.hpp: In instantiation of ‘void boost::pfr::write(std::basic_ostream<_CharT, _Traits>&, const T&) [with Char = char; Traits = std::char_traits<char>; T = my_struct]’:  
/home/mike/workspace/magic_get/include/boost/pfr/precise/ops.hpp:129:26:   required from ‘boost::pfr::detail::enable_not_ostreamable_t<std::basic_ostream<_CharT, _Traits>, T> boost::pfr::ops::operator<<(std::basic_ostream<_CharT, _Traits>&, const T&) [with Char = char; Traits = std::char_traits<char>; T = my_struct; boost::pfr::detail::enable_not_ostreamable_t<std::basic_ostream<_CharT, _Traits>, T> = std::basic_ostream<char>&]’  
source/test_magic.cpp:22:27:   required from here  
/home/mike/workspace/magic_get/include/boost/pfr/precise/io.hpp:45:47: error: invalid use of void expression  
     detail::print_impl<0, fields_count>::print(out, detail::as_tuple(value));  
     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  
    "g++"  -ftemplate-depth-128 -march=native -ftemplate-depth=256 -std=c++17 -O3 -finline-functions -Wno-inline -Wall -fPIC  -DBOOST_ALL_DYN_LINK -DNDEBUG  -I"/home/mike/workspace/magic_get/include" -I"include" -c -o "bin/gcc-7/release/source/test_magic.o" "source/test_magic.cpp"  
  
...failed gcc.compile.c++ bin/gcc-7/release/source/test_magic.o...  
```  
  
C++17 example compiler errors (gcc-7):  
  
```  
gcc.compile.c++ bin/gcc-7/release/source/test_magic.o  
In file included from /home/mike/workspace/magic_get/include/boost/pfr/detail/core17.hpp:11:0,  
                 from /home/mike/workspace/magic_get/include/boost/pfr/precise/core.hpp:20,  
                 from /home/mike/workspace/magic_get/include/boost/pfr/precise.hpp:12,  
                 from /home/mike/workspace/magic_get/include/boost/pfr.hpp:12,  
                 from source/test_magic.cpp:9:  
/home/mike/workspace/magic_get/include/boost/pfr/detail/core17_generated.hpp: In instantiation of ‘constexpr auto boost::pfr::detail::as_tuple_impl(T&&, boost::pfr::detail::size_t_<2>) [with T = const my_struct&; boost::pfr::detail::size_t_<2> = std::integral_constant<long unsigned int, 2>]’:  
/home/mike/workspace/magic_get/include/boost/pfr/detail/core17_generated.hpp:1032:43:   required from ‘constexpr auto boost::pfr::detail::as_tuple(const T&) [with T = my_struct]’  
/home/mike/workspace/magic_get/include/boost/pfr/precise/io.hpp:45:69:   required from ‘void boost::pfr::write(std::basic_ostream<_CharT, _Traits>&, const T&) [with Char = char; Traits = std::char_traits<char>; T = my_struct]’  
/home/mike/workspace/magic_get/include/boost/pfr/precise/ops.hpp:129:26:   required from ‘boost::pfr::detail::enable_not_ostreamable_t<std::basic_ostream<_CharT, _Traits>, T> boost::pfr::ops::operator<<(std::basic_ostream<_CharT, _Traits>&, const T&) [with Char = char; Traits = std::char_traits<char>; T = my_struct; boost::pfr::detail::enable_not_ostreamable_t<std::basic_ostream<_CharT, _Traits>, T> = std::basic_ostream<char>&]’  
source/test_magic.cpp:21:27:   required from here  
/home/mike/workspace/magic_get/include/boost/pfr/detail/core17_generated.hpp:51:9: error: ‘std::tuple_size<const my_struct>::value’ is not an integral constant expression  
   auto& [a,b] = std::forward<T>(val);  
         ^~~~~  
In file included from /home/mike/workspace/magic_get/include/boost/pfr/precise/ops.hpp:15:0,  
                 from /home/mike/workspace/magic_get/include/boost/pfr/precise.hpp:14,  
                 from /home/mike/workspace/magic_get/include/boost/pfr.hpp:12,  
                 from source/test_magic.cpp:9:  
/home/mike/workspace/magic_get/include/boost/pfr/precise/io.hpp: In instantiation of ‘void boost::pfr::write(std::basic_ostream<_CharT, _Traits>&, const T&) [with Char = char; Traits = std::char_traits<char>; T = my_struct]’:  
/home/mike/workspace/magic_get/include/boost/pfr/precise/ops.hpp:129:26:   required from ‘boost::pfr::detail::enable_not_ostreamable_t<std::basic_ostream<_CharT, _Traits>, T> boost::pfr::ops::operator<<(std::basic_ostream<_CharT, _Traits>&, const T&) [with Char = char; Traits = std::char_traits<char>; T = my_struct; boost::pfr::detail::enable_not_ostreamable_t<std::basic_ostream<_CharT, _Traits>, T> = std::basic_ostream<char>&]’  
source/test_magic.cpp:21:27:   required from here  
/home/mike/workspace/magic_get/include/boost/pfr/precise/io.hpp:45:47: error: invalid use of void expression  
     detail::print_impl<0, fields_count>::print(out, detail::as_tuple(value));  
     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  
    "g++"  -ftemplate-depth-128 -march=native -ftemplate-depth=256 -std=c++17 -O3 -finline-functions -Wno-inline -Wall -fPIC  -DBOOST_ALL_DYN_LINK -DNDEBUG  -I"/home/mike/workspace/magic_get/include" -I"include" -c -o "bin/gcc-7/release/source/test_magic.o" "source/test_magic.cpp"  
  
...failed gcc.compile.c++ bin/gcc-7/release/source/test_magic.o...  
```  
  
C++17 example compiler errors (clang-4):  
  
```  
clang-linux.compile.c++.without-pth bin/clang-linux-4.0.0/release/source/test_magic.o  
In file included from source/test_magic.cpp:9:  
In file included from /home/mike/workspace/magic_get/include/boost/pfr.hpp:12:  
In file included from /home/mike/workspace/magic_get/include/boost/pfr/precise.hpp:12:  
In file included from /home/mike/workspace/magic_get/include/boost/pfr/precise/core.hpp:20:  
In file included from /home/mike/workspace/magic_get/include/boost/pfr/detail/core17.hpp:11:  
/home/mike/workspace/magic_get/include/boost/pfr/detail/core17_generated.hpp:51:9: error: cannot decompose this type; 'std::tuple_size<const my_struct>::value' is not a valid integral constant expression  
  auto& [a,b] = std::forward<T>(val);  
        ^  
/home/mike/workspace/magic_get/include/boost/pfr/detail/core17_generated.hpp:1032:30: note: in instantiation of function template specialization 'boost::pfr::detail::as_tuple_impl<const my_struct &>' requested here  
  return boost::pfr::detail::as_tuple_impl(val, fields_count_tag{});  
                             ^  
/home/mike/workspace/magic_get/include/boost/pfr/precise/io.hpp:45:61: note: in instantiation of function template specialization 'boost::pfr::detail::as_tuple<my_struct>' requested here  
    detail::print_impl<0, fields_count>::print(out, detail::as_tuple(value));  
                                                            ^  
/home/mike/workspace/magic_get/include/boost/pfr/precise/ops.hpp:129:21: note: in instantiation of function template specialization 'boost::pfr::write<char, std::char_traits<char>, my_struct>' requested here  
        boost::pfr::write(out, value);  
                    ^  
source/test_magic.cpp:21:24: note: in instantiation of function template specialization 'boost::pfr::ops::operator<<<char, std::char_traits<char>, my_struct>' requested here  
        << " fields: " << s << "\n";  
                       ^  
4 errors generated.  
  
  "clang++" -c -x c++ -march=native -std=c++1z -O3 -Wno-inline -Wall -fPIC  -DBOOST_ALL_DYN_LINK -DNDEBUG -I"/home/mike/workspace/magic_get/include" -I"include" -o "bin/clang-linux-4.0.0/release/source/test_magic.o" "source/test_magic.cpp"  
  
...failed clang-linux.compile.c++.without-pth bin/clang-linux-4.0.0/release/source/test_magic.o...  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-08-19 09:22:25 UTC  
> Url: https://github.com/boostorg/pfr/issues/9#issuecomment-323512044  

Added some hints and validations that must capture the issue early.  
  
In case of GCC - you've been hit by a bug https://gcc.gnu.org/bugzilla/show_bug.cgi?id=81888  
  
Clang failures are not clear to me... What standard library are you using? What is the version of the standard library?

---

## Comment 2

> Username: octopus-prime  
> Created at: 2017-08-19 10:13:36 UTC  
> Url: https://github.com/boostorg/pfr/issues/9#issuecomment-323514456  

Ubuntu 17.04 provides the clang-4 but NOT libc++-4 :-1:   
So I have to use libstdc++-7 (from gcc) for clang builds too.

---

## Comment 3

> Username: apolukhin  
> Created at: 2017-08-19 10:39:31 UTC  
> Url: https://github.com/boostorg/pfr/issues/9#issuecomment-323515530  

Are you sure that it's libstdc++7, rather than earlier version? It seems that you've been hit by this issue https://gcc.gnu.org/bugzilla/show_bug.cgi?id=78939 of libstdc++6 and below.  
  
Could you please double check?

---

## Comment 4

> Username: octopus-prime  
> Created at: 2017-08-19 10:45:42 UTC  
> Updated at: 2017-08-19 10:51:06 UTC  
> Url: https://github.com/boostorg/pfr/issues/9#issuecomment-323515783  

I'm building with C++17 all the time.  
Using std::variant, std::optional, ...  
So I am pretty sure it's libstdc++-7.  
From my c++config.h:  
```  
// The major release number for the GCC release the C++ library belongs to.  
#define _GLIBCXX_RELEASE 7  
  
// The datestamp of the C++ library in compressed ISO date format.  
#define __GLIBCXX__ 20170407  
```  
  
BUT NOTE: I only have gcc 7.0.1 (the best Ubuntu 17.04 provides to me :-1: )

---

## Comment 5

> Username: apolukhin  
> Created at: 2017-08-22 05:32:54 UTC  
> Url: https://github.com/boostorg/pfr/issues/9#issuecomment-323923494  

Still trying to reproduce it.

---

## Comment 6

> Username: apolukhin  
> Created at: 2017-08-23 16:54:26 UTC  
> Url: https://github.com/boostorg/pfr/issues/9#issuecomment-324396935  

Could you please apply this patch https://gcc.gnu.org/viewcvs/gcc/branches/gcc-7-branch/libstdc%2B%2B-v3/include/std/utility?r1=247978&r2=247977&pathrev=247978 to your /usr/include/c++/7/utility file and try again building with clang.  
  
Looks like the issue https://gcc.gnu.org/bugzilla/show_bug.cgi?id=78939 also affects some libstdc++7 versions, but I've failed to reproduce that with clang.

---

## Comment 7

> Username: octopus-prime  
> Created at: 2017-08-23 18:48:58 UTC  
> Updated at: 2017-08-23 18:50:08 UTC  
> Url: https://github.com/boostorg/pfr/issues/9#issuecomment-324427918  

I tried the patch.  
I can compile the examples now with gcc 7.0.1 and c++17.  
Great stuff!  
  
clang still complains about some other stuff (something about std::optional).  
I think it's all about the experimental/rc1 compilers i have to use (and the missing libc++ 4.0) :(  
  
So i would suggest: Ignoring it and closing this issue - until somebody with actual compilers have the problems.  
  
Thanks!
