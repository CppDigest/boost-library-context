# #40 - Clang Memory sanitizer triggers error in string -> float conversion [Closed]

> Username: jzmaddock  
> Created at: 2021-01-02 17:56:55 UTC  
> Updated at: 2024-02-04 10:04:41 UTC  
> Closed at: 2024-02-04 10:04:40 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/40  

I haven't been able to figure out if this is a real issue or not, but it is super-annoying as it prevents running the multiprecision tests with -fsanitize=memory.  
  
If I compile:  
  
```  
#include <boost/lexical_cast.hpp>  
  
int main()  
{  
   std::string s("3.14");  
   float f = boost::lexical_cast<float>(s);  
}  
```  
  
With `clang++ -fsanitize=memory` then I see:  
  
```  
==11560==WARNING: MemorySanitizer: use-of-uninitialized-value  
    #0 0x49b367 in bool boost::detail::lexical_ostream_limited_src<char, std::char_traits<char> >::shr_using_base_class<float>(float&) /home/jzm/boost/libs/multiprecision/test/../../../boost/lexical_cast/detail/converter_lexical_streams.hpp:594:17  
    #1 0x49a4bc in bool boost::detail::lexical_ostream_limited_src<char, std::char_traits<char> >::float_types_converter_internal<float>(float&) /home/jzm/boost/libs/multiprecision/test/../../../boost/lexical_cast/detail/converter_lexical_streams.hpp:744:43  
    #2 0x499eb1 in boost::detail::lexical_ostream_limited_src<char, std::char_traits<char> >::operator>>(float&) /home/jzm/boost/libs/multiprecision/test/../../../boost/lexical_cast/detail/converter_lexical_streams.hpp:769:53  
    #3 0x499219 in boost::detail::lexical_converter_impl<float, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >::try_convert(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, float&) /home/jzm/boost/libs/multiprecision/test/../../../boost/lexical_cast/detail/converter_lexical.hpp:485:26  
    #4 0x498e39 in bool boost::conversion::detail::try_lexical_convert<float, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, float&) /home/jzm/boost/libs/multiprecision/test/../../../boost/lexical_cast/try_lexical_convert.hpp:201:20  
    #5 0x498c35 in float boost::lexical_cast<float, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) /home/jzm/boost/libs/multiprecision/test/../../../boost/lexical_cast.hpp:41:14  
    #6 0x4989ea in main /home/jzm/t.cpp:6:14  
    #7 0x7fa75f7d11e2 in __libc_start_main /build/glibc-5mDdLG/glibc-2.30/csu/../csu/libc-start.c:308:16  
    #8 0x41d4bd in _start (/home/jzm/boost/libs/multiprecision/test/a.out+0x41d4bd)  
  
SUMMARY: MemorySanitizer: use-of-uninitialized-value /home/jzm/boost/libs/multiprecision/test/../../../boost/lexical_cast/detail/converter_lexical_streams.hpp:594:17 in bool boost::detail::lexical_ostream_limited_src<char, std::char_traits<char> >::shr_using_base_class<float>(float&)  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2022-02-08 23:42:34 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/40#issuecomment-1033173298  

Line `boost/lexical_cast/detail/converter_lexical_streams.hpp:594:17` is  
```  
                stream.exceptions(std::ios::badbit);  
```  
where `stream` has just been declared as  
```  
                std::basic_istream<CharT, Traits> stream(&buf);  
```  
so this doesn't look like a legitimate `lexical_cast` issue. The documentation of -fsanitize=memory says that _all_ code needs to be instrumented, and in this case the standard library probably hasn't been.

---

## Comment 2

> Username: apolukhin  
> Created at: 2024-02-04 10:04:40 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/40#issuecomment-1925672417  

@pdimov is absolutely right - msan requires instrumentation for all the code, including C++ Standard Library, libc and c++ runtimes.  
  
We use `lexical_cast` in some projects that build and test the whole world with msan, no issues so far
