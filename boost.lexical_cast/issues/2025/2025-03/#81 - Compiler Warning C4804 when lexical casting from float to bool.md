# #81 - Compiler Warning C4804 when lexical casting from float to bool [Closed]

> Username: chrisse74  
> Created at: 2025-03-05 12:34:17 UTC  
> Updated at: 2025-10-11 14:38:15 UTC  
> Closed at: 2025-10-11 14:36:59 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/81  

When lexical casting a float to a bool, a compiler warning is issued.  
  
```  
    const double value = 1;  
    const bool result = boost::lexical_cast<bool>(value);  
    if (result)  
        std::cout << "Lexical cast successful!" << std::endl;  
    else  
        std::cout << "Lexical cast failed!" << std::endl;  
```  
  
The warning:  
```  
Boost\1.87.0\include\boost/lexical_cast/detail/converter_numeric.hpp(42): warning C4804: '<': unsafe use of type 'bool' in operation  
Boost\1.87.0\include\boost/lexical_cast/detail/converter_numeric.hpp(42): note: the template instantiation context (the oldest one first) is  
E:\devel\sharecpp\trunk\PROJECTS\Private\CMu\GeneralOperations\main.cpp(370): note: see reference to function template instantiation 'Target boost::lexical_cast<bool,double>(const Source &)' being compiled  
        with  
        [  
            Target=bool,  
            Source=double  
        ]  
Boost\1.87.0\include\boost/lexical_cast.hpp(42): note: see reference to function template instantiation 'bool boost::conversion::detail::try_lexical_convert<bool,Source>(const Source &,Target &)' being compiled  
        with  
        [  
            Source=double,  
            Target=bool  
        ]  
Boost\1.87.0\include\boost/lexical_cast/try_lexical_convert.hpp(67): note: see reference to class template instantiation 'boost::detail::dynamic_num_converter_impl<bool,src>' being compiled  
Boost\1.87.0\include\boost/lexical_cast/detail/converter_numeric.hpp(167): note: while compiling class template member function 'bool boost::detail::dynamic_num_converter_impl<bool,src>::try_convert(Source,Target &) noexcept'  
        with  
        [  
            Source=src,  
            Target=bool  
        ]  
Boost\1.87.0\include\boost/lexical_cast/try_lexical_convert.hpp(67): note: see the first reference to 'boost::detail::dynamic_num_converter_impl<bool,src>::try_convert' in 'boost::conversion::detail::try_lexical_convert'  
Boost\1.87.0\include\boost/lexical_cast/detail/converter_numeric.hpp(177): note: see reference to function template instantiation 'bool boost::detail::lexical_cast_dynamic_num_not_ignoring_minus::try_convert<Target,Source>(Source,Target &) noexcept' being compiled  
        with  
        [  
            Target=bool,  
            Source=src  
        ]  
Boost\1.87.0\include\boost/lexical_cast/detail/converter_numeric.hpp(116): note: see reference to function template instantiation 'bool boost::detail::noexcept_numeric_convert<Target,Source>(Source,Target &) noexcept' being compiled  
        with  
        [  
            Target=bool,  
            Source=src  
        ]  
Boost\1.87.0\include\boost/lexical_cast/detail/converter_numeric.hpp(104): note: see reference to function template instantiation 'bool boost::detail::ios_numeric_comparer_float<Source,Target>(Source,Source) noexcept' being compiled  
        with  
        [  
            Source=src,  
            Target=bool  
        ]  
```  
  
This was triggered under win64 with Microsoft C/C++ 19.30 in existing code using a boost::variant which contained bool and float values. In Boost 1.84.0 no such error was triggered.

---

## Comment 1

> Username: apolukhin  
> Created at: 2025-10-11 14:38:15 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/81#issuecomment-3393388907  

Many thanks for the report! Should be fixed now on develop, the fix will be in the Boost 1.90
