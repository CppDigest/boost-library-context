# #175 - MSVC Compiler 2022 C++23 compilation error [Closed]

> Username: d5ch4k  
> Created at: 2024-05-05 22:30:49 UTC  
> Updated at: 2024-10-03 01:23:48 UTC  
> Closed at: 2024-10-03 01:23:48 UTC  
> Url: https://github.com/boostorg/parser/issues/175  

The MSVC compiler 2022 C++23 cannot compile the following lines from e.g. test/transform_replace.cpp, test/split.cpp, test/search.cpp  
```  
        char const * str_ = "XYZ";  
        auto str = str_ | bp::as_utf8;  
```  
as it cannot find a suitable pipe operator  
however, using a fixed size array `char const str_[] = "XYZ"` works fine

---

## Comment 1

> Username: d5ch4k  
> Created at: 2024-05-05 23:09:05 UTC  
> Url: https://github.com/boostorg/parser/issues/175#issuecomment-2094987573  

the compiler activates `BOOST_PARSER_USE_CPP23_STD_RANGE_ADAPTOR_CLOSURE`  
```  
#if BOOST_PARSER_USE_CONCEPTS && defined(__cpp_lib_ranges) &&                  \  
    202202L <= __cpp_lib_ranges  
#define BOOST_PARSER_USE_CPP23_STD_RANGE_ADAPTOR_CLOSURE 1  
#else  
#define BOOST_PARSER_USE_CPP23_STD_RANGE_ADAPTOR_CLOSURE 0  
#endif  
```  
in view_adaptor.hpp as it returns `202302L` for `__cpp_lib_ranges`  
and switches to the stl std::ranges::_Pipe operators

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-09-30 05:00:18 UTC  
> Url: https://github.com/boostorg/parser/issues/175#issuecomment-2382089935  

The proper fix for this is probably #190.
