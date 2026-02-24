# #23 Use the MSVC 8 workaround on 9 for SFINAE fail too [Merged]

> Username: muggenhor  
> Created at: 2015-12-16 17:59:52 UTC  
> Updated at: 2015-12-21 10:24:48 UTC  
> Merged at: 2015-12-20 11:23:37 UTC  
> Closed at: 2015-12-20 11:23:37 UTC  
> Url: https://github.com/boostorg/log/pull/23  

Use the MSVC 8 workaround on 9 for SFINAE fail too  
  
Template expansion fails on MSVC 9 otherwise with:  
  
> error C2440: 'default argument' : cannot convert from 'int' to 'boost::type'  
> on the basic_formatter constructor taking a functor.  
  
For reference the full compiler error on VS 2008:  
  
```  
--------------------Configuration: Boost.Log  
Compiling...  
formatter_parser.cpp (0:07.12 at +0:07)  
boost_1_59_0\include\boost\log\expressions\formatter.hpp(156) : error C2440: 'default argument' : cannot convert from 'int' to 'boost::type'  
        The target type has no constructors  
        boost_1_59_0\libs\log\src\formatter_parser.cpp(422) : see reference to function template instantiation 'boost::log::v2s_mt_nt5::`anonymous-namespace'::chained_formatter<CharT,SecondT>::chained_formatter<boost::rv<T>>(boost::rv<T> &,const boost::rv<T> &)' being compiled  
        with  
        [  
            CharT=char,  
            SecondT=boost::log::v2s_mt_nt5::basic_formatter<char>,  
            T=boost::log::v2s_mt_nt5::basic_formatter<char>  
        ]  
        boost_1_59_0\libs\log\src\formatter_parser.cpp(401) : see reference to function template instantiation 'void boost::log::v2s_mt_nt5::`anonymous-namespace'::formatter_parser<CharT>::append_formatter<boost::log::v2s_mt_nt5::basic_formatter<CharT>>(FormatterT)' being compiled  
        with  
        [  
            CharT=char_type,  
            FormatterT=boost::log::v2s_mt_nt5::basic_formatter<char>  
        ]  
        boost_1_59_0\libs\log\src\formatter_parser.cpp(388) : while compiling class template member function 'void boost::log::v2s_mt_nt5::`anonymous-namespace'::formatter_parser<CharT>::push_attr(void)'  
        with  
        [  
            CharT=char_type  
        ]  
        boost_1_59_0\libs\log\src\formatter_parser.cpp(452) : see reference to class template instantiation 'boost::log::v2s_mt_nt5::`anonymous-namespace'::formatter_parser<CharT>' being compiled  
        with  
        [  
            CharT=char_type  
        ]  
        boost_1_59_0\libs\log\src\formatter_parser.cpp(468) : see reference to function template instantiation 'boost::log::v2s_mt_nt5::basic_formatter<CharT> boost::log::v2s_mt_nt5::parse_formatter<char>(const char *,const char *)' being compiled  
        with  
        [  
            CharT=char  
        ]  
boost_1_59_0\libs\log\src\formatter_parser.cpp(135) : fatal error C1903: unable to recover from previous error(s); stopping compilation  
```

---
