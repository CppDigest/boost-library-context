# #130 - clang-16: transcode_iterator.hpp: requires clause differs in template redeclaration [Closed]

> Username: anarthal  
> Created at: 2024-02-28 13:00:52 UTC  
> Updated at: 2024-03-15 21:29:31 UTC  
> Closed at: 2024-03-15 21:29:31 UTC  
> Url: https://github.com/boostorg/parser/issues/130  

Looks like clang is complaining about these two declarations:  
  
```  
#if BOOST_PARSER_DETAIL_TEXT_USE_CONCEPTS  
        template<  
            format FromFormat2,  
            format ToFormat2,  
            std::input_iterator I2,  
            std::sentinel_for<I2> S2,  
            transcoding_error_handler ErrorHandler2>  
        requires std::convertible_to<std::iter_value_t<I2>, detail::format_to_type_t<FromFormat2>>  
#else  
        template<  
            format FromFormat2,  
            format ToFormat2,  
            typename I2,  
            typename S2,  
            typename ErrorHandler2>  
#endif  
        friend class utf_iterator;  
```  
  
and  
  
```  
#if BOOST_PARSER_DETAIL_TEXT_USE_CONCEPTS  
    template<  
        format FromFormat,  
        format ToFormat,  
        std::input_iterator I,  
        std::sentinel_for<I> S,  
        transcoding_error_handler ErrorHandler>  
        requires std::convertible_to<std::iter_value_t<I>, detail::format_to_type_t<FromFormat>>  
#else  
    template<  
        format FromFormat,  
        format ToFormat,  
        typename I,  
        typename S,  
        typename ErrorHandler>  
#endif  
    class utf_iterator  
```  
  
I'd say they are identical except for template names. I don't know whether the standard forces you to use the same names or it's a clang bug.  
  
Failing build: https://drone.cpp.al/boostorg/mysql/542/16/3  
  
Compiler details:  
* toolset=clang-16  
* cxxstd=20  
* variant=debug  
* Building with asan and ubsan (although I don't think it makes a difference)  
* OS: Ubuntu 22.04
