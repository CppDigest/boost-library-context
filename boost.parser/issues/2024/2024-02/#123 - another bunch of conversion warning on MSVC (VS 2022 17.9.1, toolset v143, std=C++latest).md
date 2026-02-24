# #123 - another bunch of conversion warning on MSVC (VS 2022 17.9.1, toolset v143, std=C++latest) [Closed]

> Username: tobias-loew  
> Created at: 2024-02-27 15:25:46 UTC  
> Updated at: 2024-03-15 21:29:32 UTC  
> Closed at: 2024-03-15 21:29:32 UTC  
> Url: https://github.com/boostorg/parser/issues/123  

I just pasted the compiler output  
  
Build started at 16:23...  
1>------ Build started: Project: ConsoleApplication44, Configuration: Debug x64 ------  
1>ConsoleApplication44.cpp  
1>C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\parser.hpp(1517,41): warning C4244: '=': conversion from 'ptrdiff_t' to 'int', possible loss of data  
1>(compiling source file 'ConsoleApplication44.cpp')  
1>C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\parser.hpp(1517,41):  
1>the template instantiation context (the oldest one first) is  
1>	C:\Users\k8705963\source\repos\ConsoleApplication44\ConsoleApplication44\ConsoleApplication44.cpp(608,9):  
1>	see reference to function template instantiation 'std::optional<attr_t> boost::parser::parse<boost::parser::detail::text::utf32_view<std::ranges::subrange<I,S,std::ranges::subrange_kind::sized>>,boost::parser::string_parser<R,boost::parser::detail::text::null_sentinel_t>,boost::parser::detail::nope,boost::parser::default_error_handler>(const boost::parser::detail::text::utf32_view<std::ranges::subrange<I,S,std::ranges::subrange_kind::sized>> &,const boost::parser::parser_interface<boost::parser::string_parser<R,boost::parser::detail::text::null_sentinel_t>,boost::parser::detail::nope,boost::parser::default_error_handler> &,boost::parser::trace)' being compiled  
1>        with  
1>        [  
1>            I=const char *,  
1>            S=const char *,  
1>            R=const char8_t *  
1>        ]  
1>	C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\parser.hpp(7730,34):  
1>	see reference to function template instantiation 'std::optional<attr_t> boost::parser::prefix_parse<boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,I,S,boost::parser::detail::text::use_replacement_character>,boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,I,S,boost::parser::detail::text::use_replacement_character>,boost::parser::string_parser<R,boost::parser::detail::text::null_sentinel_t>,boost::parser::detail::nope,boost::parser::default_error_handler>(boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,I,S,boost::parser::detail::text::use_replacement_character> &,boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,I,S,boost::parser::detail::text::use_replacement_character>,const boost::parser::parser_interface<boost::parser::string_parser<R,boost::parser::detail::text::null_sentinel_t>,boost::parser::detail::nope,boost::parser::default_error_handler> &,boost::parser::trace)' being compiled  
1>        with  
1>        [  
1>            I=const char *,  
1>            S=const char *,  
1>            R=const char8_t *  
1>        ]  
1>	C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\parser.hpp(7671,32):  
1>	see reference to function template instantiation 'std::optional<attr_t> boost::parser::detail::parse_impl<true,I,S,boost::parser::parser_interface<boost::parser::string_parser<R,boost::parser::detail::text::null_sentinel_t>,boost::parser::detail::nope,boost::parser::default_error_handler>,boost::parser::default_error_handler>(Iter &,Sentinel,const Parser &,const ErrorHandler &)' being compiled  
1>        with  
1>        [  
1>            I=boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char *,const char *,boost::parser::detail::text::use_replacement_character>,  
1>            S=boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char *,const char *,boost::parser::detail::text::use_replacement_character>,  
1>            R=const char8_t *,  
1>            Iter=boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char *,const char *,boost::parser::detail::text::use_replacement_character>,  
1>            Sentinel=boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char *,const char *,boost::parser::detail::text::use_replacement_character>,  
1>            Parser=boost::parser::parser_interface<boost::parser::string_parser<const char8_t *,boost::parser::detail::text::null_sentinel_t>,boost::parser::detail::nope,boost::parser::default_error_handler>,  
1>            ErrorHandler=boost::parser::default_error_handler  
1>        ]  
1>	C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\parser.hpp(2131,9):  
1>	see reference to function template instantiation 'std::string boost::parser::parser_interface<boost::parser::string_parser<R,boost::parser::detail::text::null_sentinel_t>,boost::parser::detail::nope,boost::parser::default_error_handler>::operator ()<false,Iter,Sentinel,boost::parser::detail::parse_context<Iter,Sentinel,ErrorHandler,GlobalState,boost::parser::detail::nope,boost::parser::detail::nope,boost::parser::detail::nope,void,boost::parser::detail::nope,boost::parser::detail::nope,boost::parser::detail::nope>,boost::parser::detail::null_parser>(std::integral_constant<bool,false>,Iter &,Sentinel,const Context &,const SkipParserType &,boost::parser::detail::flags,bool &) const' being compiled  
1>        with  
1>        [  
1>            R=const char8_t *,  
1>            Iter=boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char *,const char *,boost::parser::detail::text::use_replacement_character>,  
1>            Sentinel=boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char *,const char *,boost::parser::detail::text::use_replacement_character>,  
1>            ErrorHandler=boost::parser::default_error_handler,  
1>            GlobalState=const boost::parser::detail::nope,  
1>            Context=boost::parser::detail::parse_context<boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char *,const char *,boost::parser::detail::text::use_replacement_character>,boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char *,const char *,boost::parser::detail::text::use_replacement_character>,boost::parser::default_error_handler,const boost::parser::detail::nope,boost::parser::detail::nope,boost::parser::detail::nope,boost::parser::detail::nope,void,boost::parser::detail::nope,boost::parser::detail::nope,boost::parser::detail::nope>,  
1>            SkipParserType=boost::parser::detail::null_parser  
1>        ]  
1>	C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\parser.hpp(5110,27):  
1>	see reference to function template instantiation 'std::string boost::parser::string_parser<R,boost::parser::detail::text::null_sentinel_t>::call<false,Iter,Sentinel,Context,SkipParserType>(std::integral_constant<bool,false>,Iter &,Sentinel,const Context &,const SkipParser &,boost::parser::detail::flags,bool &) const' being compiled  
1>        with  
1>        [  
1>            R=const char8_t *,  
1>            Iter=boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char *,const char *,boost::parser::detail::text::use_replacement_character>,  
1>            Sentinel=boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char *,const char *,boost::parser::detail::text::use_replacement_character>,  
1>            Context=boost::parser::detail::parse_context<boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char *,const char *,boost::parser::detail::text::use_replacement_character>,boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char *,const char *,boost::parser::detail::text::use_replacement_character>,boost::parser::default_error_handler,const boost::parser::detail::nope,boost::parser::detail::nope,boost::parser::detail::nope,boost::parser::detail::nope,void,boost::parser::detail::nope,boost::parser::detail::nope,boost::parser::detail::nope>,  
1>            SkipParserType=boost::parser::detail::null_parser,  
1>            SkipParser=boost::parser::detail::null_parser  
1>        ]  
1>	C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\parser.hpp(6397,13):  
1>	see reference to function template instantiation 'void boost::parser::string_parser<R,boost::parser::detail::text::null_sentinel_t>::call<false,Iter,Sentinel,Context,SkipParser,std::string>(std::integral_constant<bool,false>,Iter &,Sentinel,const Context &,const SkipParser &,boost::parser::detail::flags,bool &,Attribute &) const' being compiled  
1>        with  
1>        [  
1>            R=const char8_t *,  
1>            Iter=boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char *,const char *,boost::parser::detail::text::use_replacement_character>,  
1>            Sentinel=boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char *,const char *,boost::parser::detail::text::use_replacement_character>,  
1>            Context=boost::parser::detail::parse_context<boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char *,const char *,boost::parser::detail::text::use_replacement_character>,boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char *,const char *,boost::parser::detail::text::use_replacement_character>,boost::parser::default_error_handler,const boost::parser::detail::nope,boost::parser::detail::nope,boost::parser::detail::nope,boost::parser::detail::nope,void,boost::parser::detail::nope,boost::parser::detail::nope,boost::parser::detail::nope>,  
1>            SkipParser=boost::parser::detail::null_parser,  
1>            Attribute=std::string  
1>        ]  
1>	C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\parser.hpp(6433,47):  
1>	see reference to function template instantiation 'std::pair<Iter,boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,I,S,boost::parser::detail::text::use_replacement_character>> boost::parser::detail::no_case_aware_string_mismatch<Iter,Sentinel,boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,I,S,boost::parser::detail::text::use_replacement_character>,S>(Iter1,Sentinel1,Iter2,Sentinel2,bool)' being compiled  
1>        with  
1>        [  
1>            Iter=boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char *,const char *,boost::parser::detail::text::use_replacement_character>,  
1>            I=const char8_t *,  
1>            S=boost::parser::detail::text::null_sentinel_t,  
1>            Sentinel=boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char *,const char *,boost::parser::detail::text::use_replacement_character>,  
1>            Iter1=boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char *,const char *,boost::parser::detail::text::use_replacement_character>,  
1>            Sentinel1=boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char *,const char *,boost::parser::detail::text::use_replacement_character>,  
1>            Iter2=boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char8_t *,boost::parser::detail::text::null_sentinel_t,boost::parser::detail::text::use_replacement_character>,  
1>            Sentinel2=boost::parser::detail::text::null_sentinel_t  
1>        ]  
1>	C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\parser.hpp(2486,40):  
1>	see reference to class template instantiation 'boost::parser::detail::no_case_iter<Iter2,Sentinel2>' being compiled  
1>        with  
1>        [  
1>            Iter2=boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char8_t *,boost::parser::detail::text::null_sentinel_t,boost::parser::detail::text::use_replacement_character>,  
1>            Sentinel2=boost::parser::detail::text::null_sentinel_t  
1>        ]  
1>	C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\parser.hpp(1507,18):  
1>	while compiling class template member function 'void boost::parser::detail::no_case_iter<Iter2,Sentinel2>::fold(void)'  
1>        with  
1>        [  
1>            Iter2=boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char8_t *,boost::parser::detail::text::null_sentinel_t,boost::parser::detail::text::use_replacement_character>,  
1>            Sentinel2=boost::parser::detail::text::null_sentinel_t  
1>        ]  
1>		C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\parser.hpp(1476,21):  
1>		see the first reference to 'boost::parser::detail::no_case_iter<Iter2,Sentinel2>::fold' in 'boost::parser::detail::no_case_iter<Iter2,Sentinel2>::no_case_iter'  
1>        with  
1>        [  
1>            Iter2=boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char8_t *,boost::parser::detail::text::null_sentinel_t,boost::parser::detail::text::use_replacement_character>,  
1>            Sentinel2=boost::parser::detail::text::null_sentinel_t  
1>        ]  
1>		C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\parser.hpp(2486,40):  
1>		see the first reference to 'boost::parser::detail::no_case_iter<Iter2,Sentinel2>::no_case_iter' in 'boost::parser::detail::no_case_aware_string_mismatch'  
1>        with  
1>        [  
1>            Iter2=boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,const char8_t *,boost::parser::detail::text::null_sentinel_t,boost::parser::detail::text::use_replacement_character>,  
1>            Sentinel2=boost::parser::detail::text::null_sentinel_t  
1>        ]  
1>C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\detail\text\transcode_iterator.hpp(386,35): warning C4244: '=': conversion from '__int64' to 'int', possible loss of data  
1>(compiling source file 'ConsoleApplication44.cpp')  
1>C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\detail\text\transcode_iterator.hpp(386,35):  
1>the template instantiation context (the oldest one first) is  
1>	C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\detail\text\transcode_view.hpp(761,27):  
1>	see reference to variable template 'bool can_utf_view<std::ranges::subrange<char const *,char const *,1>,boost::parser::detail::text::utf32_view>' being compiled  
1>	C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\detail\text\transcode_view.hpp(702,62):  
1>	see reference to class template instantiation 'boost::parser::detail::text::utf32_view<std::ranges::subrange<I,S,std::ranges::subrange_kind::sized>>' being compiled  
1>        with  
1>        [  
1>            I=const char *,  
1>            S=const char *  
1>        ]  
1>	C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\detail\text\transcode_view.hpp(662,31):  
1>	see reference to class template instantiation 'boost::parser::detail::text::utf_view<boost::parser::detail::text::format::utf32,V>' being compiled  
1>        with  
1>        [  
1>            V=std::ranges::subrange<const char *,const char *,std::ranges::subrange_kind::sized>  
1>        ]  
1>	C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\detail\text\transcode_view.hpp(533,24):  
1>	while compiling class template member function 'boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,I,S,boost::parser::detail::text::use_replacement_character> boost::parser::detail::text::utf_view<boost::parser::detail::text::format::utf32,V>::begin(void)'  
1>        with  
1>        [  
1>            I=const char *,  
1>            S=const char *,  
1>            V=std::ranges::subrange<const char *,const char *,std::ranges::subrange_kind::sized>  
1>        ]  
1>	C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\detail\text\transcode_view.hpp(539,24):  
1>	see reference to function template instantiation 'boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,I,S,boost::parser::detail::text::use_replacement_character> boost::parser::detail::text::utf_view<boost::parser::detail::text::format::utf32,V>::make_begin<boost::parser::detail::text::format::utf8,_It,_Se>(I,S)' being compiled  
1>        with  
1>        [  
1>            I=const char *,  
1>            S=const char *,  
1>            V=std::ranges::subrange<const char *,const char *,std::ranges::subrange_kind::sized>,  
1>            _It=const char *,  
1>            _Se=const char *  
1>        ]  
1>	C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\detail\text\transcode_view.hpp(503,62):  
1>	see reference to class template instantiation 'boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,I,S,boost::parser::detail::text::use_replacement_character>' being compiled  
1>        with  
1>        [  
1>            I=const char *,  
1>            S=const char *  
1>        ]  
1>	C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\detail\text\transcode_iterator.hpp(3056,28):  
1>	while compiling class template member function 'char32_t boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,I,S,boost::parser::detail::text::use_replacement_character>::decode_code_point_reverse(void)'  
1>        with  
1>        [  
1>            I=const char *,  
1>            S=const char *  
1>        ]  
1>		C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\detail\text\transcode_iterator.hpp(3180,29):  
1>		see the first reference to 'boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,I,S,boost::parser::detail::text::use_replacement_character>::decode_code_point_reverse' in 'boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,I,S,boost::parser::detail::text::use_replacement_character>::read_reverse'  
1>        with  
1>        [  
1>            I=const char *,  
1>            S=const char *  
1>        ]  
1>		C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\detail\text\transcode_iterator.hpp(2747,29):  
1>		see the first reference to 'boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,I,S,boost::parser::detail::text::use_replacement_character>::read_reverse' in 'boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,I,S,boost::parser::detail::text::use_replacement_character>::operator --'  
1>        with  
1>        [  
1>            I=const char *,  
1>            S=const char *  
1>        ]  
1>		C:\Program Files\Microsoft Visual Studio\2022\Professional\VC\Tools\MSVC\14.39.33519\include\xutility(1435,17):  
1>		see the first reference to 'boost::parser::detail::text::utf_iterator<boost::parser::detail::text::format::utf8,boost::parser::detail::text::format::utf32,I,S,boost::parser::detail::text::use_replacement_character>::operator --' in 'std::advance'  
1>        with  
1>        [  
1>            I=const char *,  
1>            S=const char *  
1>        ]  
1>	C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\detail\text\transcode_iterator.hpp(3062,34):  
1>	see reference to function template instantiation 'Iter boost::parser::detail::text::detail::decrement<I>(Iter,Iter)' being compiled  
1>        with  
1>        [  
1>            Iter=const char *,  
1>            I=const char *  
1>        ]  
1>C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\detail\text\transcode_iterator.hpp(399,40): warning C4244: '-=': conversion from '__int64' to 'int', possible loss of data  
1>(compiling source file 'ConsoleApplication44.cpp')  
1>Done building project "ConsoleApplication44.vcxproj".  
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========  
========== Build completed at 16:23 and took 03,809 seconds ==========

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-28 05:02:15 UTC  
> Url: https://github.com/boostorg/parser/issues/123#issuecomment-1968234502  

Ok, I acknowledge this issue and all the other MSVC warnings issue.  Sigh.  I'll try to mitigate as much of these as I can.
