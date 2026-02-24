# #132 - Build failure with MSVC 14.2 and 14.3 under C++17 [Closed]

> Username: anarthal  
> Created at: 2024-02-28 14:46:40 UTC  
> Updated at: 2024-03-30 18:49:18 UTC  
> Closed at: 2024-03-30 18:49:18 UTC  
> Url: https://github.com/boostorg/parser/issues/132  

Build logs can be found here (edited links):  
* https://drone.cpp.al/boostorg/mysql/545/26/2 (MSVC 14.2)  
* https://drone.cpp.al/boostorg/mysql/545/27/2 (MSVC 14.3)  
  
This happens only in C++17 - C++20 is fine. I'm finding difficult to parse the errors and diagnose what's going on, and I don't have easy access to Windows. This is the part of the log I think is relevant:  
  
```  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/unpack.hpp(180): error C2338: static_assert failed: 'Unpacked iterator is not a utf_iter!'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/unpack.hpp(105): note: see reference to function template instantiation 'auto boost::parser::detail::text::detail::unpack_iterator_and_sentinel_impl<I,S,Repack>(I,S,Repack)' being compiled  
  
        with  
  
        [  
  
            I=std::_Array_const_iterator<const char,63>,  
  
            S=std::_Array_const_iterator<const char,63>,  
  
            Repack=boost::parser::detail::text::no_op_repacker  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/unpack.hpp(123): note: see reference to function template instantiation 'auto boost::parser::detail::text::detail::unpack_iterator_and_sentinel<I,S,Repack>(I,S,Repack)' being compiled  
  
        with  
  
        [  
  
            I=std::_Array_const_iterator<const char,63>,  
  
            S=std::_Array_const_iterator<const char,63>,  
  
            Repack=boost::parser::detail::text::no_op_repacker  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(775): note: see reference to function template instantiation 'auto boost::parser::detail::text::detail::unpack_iterator_and_sentinel_cpo::operator ()<std::_Array_const_iterator<_Ty,63>,std::_Array_const_iterator<_Ty,63>,boost::parser::detail::text::no_op_repacker>(I,S,Repack) const' being compiled  
  
        with  
  
        [  
  
            _Ty=const char,  
  
            I=std::_Array_const_iterator<const char,63>,  
  
            S=std::_Array_const_iterator<const char,63>,  
  
            Repack=boost::parser::detail::text::no_op_repacker  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(775): note: see reference to function template instantiation 'decltype(auto) boost::parser::detail::text::detail::unpack_range<boost::parser::subrange<std::_Array_const_iterator<_Ty,63>,std::_Array_const_iterator<_Ty,63>>&>(R)' being compiled  
  
        with  
  
        [  
  
            _Ty=const char,  
  
            R=boost::parser::subrange<std::_Array_const_iterator<const char,63>,std::_Array_const_iterator<const char,63>> &  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/stl_interfaces/view_adaptor.hpp(55): note: see reference to function template instantiation 'auto boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view,boost::parser::detail::text::format::utf8>::operator ()<boost::parser::subrange<std::_Array_const_iterator<_Ty,63>,std::_Array_const_iterator<_Ty,63>>&>(R) const' being compiled  
  
        with  
  
        [  
  
            _Ty=const char,  
  
            R=boost::parser::subrange<std::_Array_const_iterator<const char,63>,std::_Array_const_iterator<const char,63>> &  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/unpack.hpp(105): note: see reference to alias template instantiation 'boost::parser::detail::stl_interfaces::detail::invocable_expr<D,T>' being compiled  
  
        with  
  
        [  
  
            D=boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view,boost::parser::detail::text::format::utf8>,  
  
            T=boost::parser::subrange<std::_Array_const_iterator<const char,63>,std::_Array_const_iterator<const char,63>> &  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/detection.hpp(75): note: see reference to alias template instantiation 'boost::parser::detail::is_detected<boost::parser::detail::stl_interfaces::detail::invocable_expr,D,T>' being compiled  
  
        with  
  
        [  
  
            D=boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view,boost::parser::detail::text::format::utf8>,  
  
            T=boost::parser::subrange<std::_Array_const_iterator<const char,63>,std::_Array_const_iterator<const char,63>> &  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/stl_interfaces/view_adaptor.hpp(58): note: see reference to variable template 'const bool is_detected_v<boost::parser::detail::stl_interfaces::detail::invocable_expr,boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view,1>,boost::parser::subrange<std::_Array_const_iterator<char const ,63>,std::_Array_const_iterator<char const ,63> > &>' being compiled  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/stl_interfaces/view_adaptor.hpp(199): note: see reference to variable template 'const bool is_invocable_v<boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view,1>,boost::parser::subrange<std::_Array_const_iterator<char const ,63>,std::_Array_const_iterator<char const ,63> > &>' being compiled  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/printing_impl.hpp(510): note: while compiling class template member function 'void boost::parser::detail::char_print_parser_impl<Context,char_range_t>::call(const Context &,std::ostream &,boost::parser::detail::char_range<std::_Array_const_iterator<_Ty,63>,std::_Array_const_iterator<_Ty,63>,false>)'  
  
        with  
  
        [  
  
            Context=boost::parser::detail::parse_context<const char *,const char *,boost::parser::callback_error_handler,boost::mysql::detail::i_didnt_test_copies,boost::parser::detail::nope,boost::parser::detail::nope,boost::parser::detail::nope,void,boost::parser::detail::nope,boost::parser::detail::nope,boost::parser::detail::nope>,  
  
            _Ty=const char  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/printing_impl.hpp(535): note: see reference to function template instantiation 'void boost::parser::detail::char_print_parser_impl<Context,char_range_t>::call(const Context &,std::ostream &,boost::parser::detail::char_range<std::_Array_const_iterator<_Ty,63>,std::_Array_const_iterator<_Ty,63>,false>)' being compiled  
  
        with  
  
        [  
  
            Context=boost::parser::detail::parse_context<const char *,const char *,boost::parser::callback_error_handler,boost::mysql::detail::i_didnt_test_copies,boost::parser::detail::nope,boost::parser::detail::nope,boost::parser::detail::nope,void,boost::parser::detail::nope,boost::parser::detail::nope,boost::parser::detail::nope>,  
  
            _Ty=const char  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/printing_impl.hpp(526): note: see reference to class template instantiation 'boost::parser::detail::char_print_parser_impl<Context,char_range_t>' being compiled  
  
        with  
  
        [  
  
            Context=boost::parser::detail::parse_context<const char *,const char *,boost::parser::callback_error_handler,boost::mysql::detail::i_didnt_test_copies,boost::parser::detail::nope,boost::parser::detail::nope,boost::parser::detail::nope,void,boost::parser::detail::nope,boost::parser::detail::nope,boost::parser::detail::nope>  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/printing_impl.hpp(102): note: see reference to function template instantiation 'void boost::parser::detail::print_parser<Context,char_range_t,AttributeType>(const Context &,const boost::parser::char_parser<char_range_t,AttributeType> &,std::ostream &,int)' being compiled  
  
        with  
  
        [  
  
            Context=boost::parser::detail::parse_context<const char *,const char *,boost::parser::callback_error_handler,boost::mysql::detail::i_didnt_test_copies,boost::parser::detail::nope,boost::parser::detail::nope,boost::parser::detail::nope,void,boost::parser::detail::nope,boost::parser::detail::nope,boost::parser::detail::nope>,  
  
            AttributeType=void  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(721): error C2062: type 'unknown-type' unexpected  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(721): error C2143: syntax error: missing ';' before '{'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(721): error C2181: illegal else without matching if  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(721): error C3487: '_Ty': all return expressions must deduce to the same type: previously it was 'boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<_Ty,63>,std::_Array_const_iterator<_Ty,63>>>'  
  
        with  
  
        [  
  
            _Ty=boost::parser::subrange<std::_Array_const_iterator<const char,63>,std::_Array_const_iterator<const char,63>> &  
  
        ]  
  
        and  
  
        [  
  
            _Ty=const char  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(467): error C2641: cannot deduce template arguments for 'boost::parser::subrange'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(465): note: while compiling class template member function 'auto boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<_Ty,63>,std::_Array_const_iterator<_Ty,63>>>::code_units(void) noexcept const'  
  
        with  
  
        [  
  
            _Ty=const char  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(721): note: see reference to class template instantiation 'boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<_Ty,63>,std::_Array_const_iterator<_Ty,63>>>' being compiled  
  
        with  
  
        [  
  
            _Ty=const char  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(775): note: see reference to function template instantiation 'boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<_Ty,63>,std::_Array_const_iterator<_Ty,63>>> boost::parser::detail::text::detail::unpack_range<boost::parser::subrange<std::_Array_const_iterator<_Ty,63>,std::_Array_const_iterator<_Ty,63>>&>(R)' being compiled  
  
        with  
  
        [  
  
            _Ty=const char,  
  
            R=boost::parser::subrange<std::_Array_const_iterator<const char,63>,std::_Array_const_iterator<const char,63>> &  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(470): note: while compiling class template member function 'auto boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<_Ty,63>,std::_Array_const_iterator<_Ty,63>>>::begin(void)'  
  
        with  
  
        [  
  
            _Ty=const char  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(467): note: see reference to alias template instantiation 'boost::parser::detail::text::detail::begin_impl::member_begin_expr<R&>' being compiled  
  
        with  
  
        [  
  
            R=boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<const char,63>,std::_Array_const_iterator<const char,63>>>  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/detection.hpp(75): note: see reference to alias template instantiation 'boost::parser::detail::is_detected<boost::parser::detail::text::detail::begin_impl::member_begin_expr,R&>' being compiled  
  
        with  
  
        [  
  
            R=boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<const char,63>,std::_Array_const_iterator<const char,63>>>  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/detail/begin_end.hpp(38): note: see reference to variable template 'const bool is_detected_v<boost::parser::detail::text::detail::begin_impl::member_begin_expr,boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<char const ,63>,std::_Array_const_iterator<char const ,63> > > &>' being compiled  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(467): note: see reference to variable template 'const bool has_member_begin_v<boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<char const ,63>,std::_Array_const_iterator<char const ,63> > > &>' being compiled  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/detail/begin_end.hpp(63): note: while compiling class template member function 'enable_if<boost::parser::detail::text::detail::begin_impl::has_member_begin_v<R>,unknown-type>::type boost::parser::detail::text::detail::begin_impl::impl::operator ()(R &&) const'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(467): note: see reference to alias template instantiation 'boost::parser::detail::text::detail::iterator_<R>' being compiled  
  
        with  
  
        [  
  
            R=boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<const char,63>,std::_Array_const_iterator<const char,63>>>  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/detection.hpp(75): note: see reference to alias template instantiation 'boost::parser::detail::is_detected<boost::parser::detail::text::detail::iterator_,R>' being compiled  
  
        with  
  
        [  
  
            R=boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<const char,63>,std::_Array_const_iterator<const char,63>>>  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/detail/all_t.hpp(28): note: see reference to variable template 'const bool is_detected_v<boost::parser::detail::text::detail::iterator_,boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<char const ,63>,std::_Array_const_iterator<char const ,63> > > >' being compiled  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/detail/all_t.hpp(119): note: see reference to variable template 'const bool range_<boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<char const ,63>,std::_Array_const_iterator<char const ,63> > > >' being compiled  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(478): note: see reference to alias template instantiation 'boost::parser::detail::text::detail::all_t<boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<_Ty,63>,std::_Array_const_iterator<_Ty,63>>>>' being compiled  
  
        with  
  
        [  
  
            _Ty=const char  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(669): note: see reference to function template instantiation 'boost::parser::detail::text::utf8_view<unknown-type> boost::parser::detail::text::utf8_view(R &&)' being compiled  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(467): error C2783: 'boost::parser::subrange<I,S> boost::parser::subrange(void)': could not deduce template argument for 'I'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/subrange.hpp(26): note: see declaration of 'boost::parser::subrange'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(467): error C2780: 'boost::parser::subrange<I,S> boost::parser::subrange(boost::parser::subrange<I,S>)': expects 1 arguments - 0 provided  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/subrange.hpp(24): note: see declaration of 'boost::parser::subrange'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/concepts.hpp(243): error C3889: call to object of class type 'boost::parser::detail::text::detail::begin_impl::impl': no matching call operator found  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/detail/begin_end.hpp(52): note: could be 'void boost::parser::detail::text::detail::begin_impl::impl::operator ()(R (&&)[N]) const'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/concepts.hpp(243): note: 'void boost::parser::detail::text::detail::begin_impl::impl::operator ()(R (&&)[N]) const': could not deduce template argument for 'R (&&)[N]' from 'const V'  
  
        with  
  
        [  
  
            V=boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<const char,63>,std::_Array_const_iterator<const char,63>>>  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/detail/begin_end.hpp(52): note: see declaration of 'boost::parser::detail::text::detail::begin_impl::impl::operator ()'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/detail/begin_end.hpp(55): note: or       'R *boost::parser::detail::text::detail::begin_impl::impl::operator ()(R (&)[N]) const'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/concepts.hpp(243): note: 'R *boost::parser::detail::text::detail::begin_impl::impl::operator ()(R (&)[N]) const': could not deduce template argument for 'R (&)[N]' from 'const V'  
  
        with  
  
        [  
  
            V=boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<const char,63>,std::_Array_const_iterator<const char,63>>>  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/detail/begin_end.hpp(55): note: see declaration of 'boost::parser::detail::text::detail::begin_impl::impl::operator ()'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/detail/begin_end.hpp(63): note: or       'enable_if<boost::parser::detail::text::detail::begin_impl::has_member_begin_v<R>,unknown-type>::type boost::parser::detail::text::detail::begin_impl::impl::operator ()(R &&) const'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/concepts.hpp(243): note: Failed to specialize function template 'enable_if<boost::parser::detail::text::detail::begin_impl::has_member_begin_v<R>,unknown-type>::type boost::parser::detail::text::detail::begin_impl::impl::operator ()(R &&) const'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/detail/begin_end.hpp(63): note: see declaration of 'boost::parser::detail::text::detail::begin_impl::impl::operator ()'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/concepts.hpp(243): note: With the following template arguments:  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/concepts.hpp(243): note: 'R=const V &'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/detail/begin_end.hpp(72): note: or       'enable_if<!boost::parser::detail::text::detail::begin_impl::has_member_begin_v<R>&&boost::parser::detail::text::detail::begin_impl::has_adl_begin_v<R>,unknown-type>::type boost::parser::detail::text::detail::begin_impl::impl::operator ()(R &&) const'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/concepts.hpp(243): note: Failed to specialize function template 'enable_if<!boost::parser::detail::text::detail::begin_impl::has_member_begin_v<R>&&boost::parser::detail::text::detail::begin_impl::has_adl_begin_v<R>,unknown-type>::type boost::parser::detail::text::detail::begin_impl::impl::operator ()(R &&) const'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/detail/begin_end.hpp(72): note: see declaration of 'boost::parser::detail::text::detail::begin_impl::impl::operator ()'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/concepts.hpp(243): note: With the following template arguments:  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/concepts.hpp(243): note: 'R=const V &'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/concepts.hpp(251): note: see reference to alias template instantiation 'boost::parser::detail::text::detail::iterator_t<const V>' being compiled  
  
        with  
  
        [  
  
            V=boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<const char,63>,std::_Array_const_iterator<const char,63>>>  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(536): note: see reference to alias template instantiation 'boost::parser::detail::text::detail::range_value_t<const V>' being compiled  
  
        with  
  
        [  
  
            V=boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<const char,63>,std::_Array_const_iterator<const char,63>>>  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(535): note: while compiling class template member function 'auto boost::parser::detail::text::utf_view<boost::parser::detail::text::format::utf8,V>::begin(void) const'  
  
        with  
  
        [  
  
            V=boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<const char,63>,std::_Array_const_iterator<const char,63>>>  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(619): note: see reference to class template instantiation 'boost::parser::detail::text::utf_view<boost::parser::detail::text::format::utf8,V>' being compiled  
  
        with  
  
        [  
  
            V=boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<const char,63>,std::_Array_const_iterator<const char,63>>>  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(775): note: see reference to class template instantiation 'boost::parser::detail::text::utf8_view<boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<_Ty,63>,std::_Array_const_iterator<_Ty,63>>>>' being compiled  
  
        with  
  
        [  
  
            _Ty=const char  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/stl_interfaces/view_adaptor.hpp(55): note: see reference to function template instantiation 'boost::parser::detail::text::utf8_view<boost::parser::detail::text::unpacking_view<boost::parser::subrange<std::_Array_const_iterator<_Ty,63>,std::_Array_const_iterator<_Ty,63>>>> boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view,boost::parser::detail::text::format::utf8>::operator ()<boost::parser::subrange<std::_Array_const_iterator<_Ty,63>,std::_Array_const_iterator<_Ty,63>>&>(R) const' being compiled  
  
        with  
  
        [  
  
            _Ty=const char,  
  
            R=boost::parser::subrange<std::_Array_const_iterator<const char,63>,std::_Array_const_iterator<const char,63>> &  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/concepts.hpp(243): note: see reference to alias template instantiation 'boost::parser::detail::stl_interfaces::detail::invocable_expr<D,T>' being compiled  
  
        with  
  
        [  
  
            D=boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view,boost::parser::detail::text::format::utf8>,  
  
            T=boost::parser::subrange<std::_Array_const_iterator<const char,63>,std::_Array_const_iterator<const char,63>> &  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/detection.hpp(75): note: see reference to alias template instantiation 'boost::parser::detail::is_detected<boost::parser::detail::stl_interfaces::detail::invocable_expr,D,T>' being compiled  
  
        with  
  
        [  
  
            D=boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view,boost::parser::detail::text::format::utf8>,  
  
            T=boost::parser::subrange<std::_Array_const_iterator<const char,63>,std::_Array_const_iterator<const char,63>> &  
  
        ]  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/stl_interfaces/view_adaptor.hpp(58): note: see reference to variable template 'const bool is_detected_v<boost::parser::detail::stl_interfaces::detail::invocable_expr,boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view,1>,boost::parser::subrange<std::_Array_const_iterator<char const ,63>,std::_Array_const_iterator<char const ,63> > &>' being compiled  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/stl_interfaces/view_adaptor.hpp(199): note: see reference to variable template 'const bool is_invocable_v<boost::parser::detail::text::detail::as_utf_impl<boost::parser::detail::text::utf8_view,1>,boost::parser::subrange<std::_Array_const_iterator<char const ,63>,std::_Array_const_iterator<char const ,63> > &>' being compiled  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/utf.hpp(30): error C2059: syntax error: ')'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(536): note: see reference to function template instantiation 'boost::parser::detail::text::format boost::parser::detail::text::detail::format_of<unknown-type>(void)' being compiled  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/utf.hpp(24): error C2143: syntax error: missing ';' before '{'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/utf.hpp(24): error C2181: illegal else without matching if  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/utf.hpp(35): error C2059: syntax error: '||'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/utf.hpp(42): error C2059: syntax error: '}'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(537): error C2065: 'V': undeclared identifier  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(537): error C2923: 'boost::parser::detail::text::detail::is_charn_view': 'V' is not a valid template type argument for parameter 'T'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(537): note: see declaration of 'V'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(537): error C2143: syntax error: missing ';' before '{'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(537): error C2447: '{': missing function header (old-style formal list?)  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(539): error C2059: syntax error: 'else'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(539): error C2143: syntax error: missing ';' before '{'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(539): error C2447: '{': missing function header (old-style formal list?)  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(542): error C2059: syntax error: '}'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/text/transcode_view.hpp(542): error C2143: syntax error: missing ';' before '}'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/printing_impl.hpp(513): error C2065: '<range>$L0': undeclared identifier  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/printing_impl.hpp(513): error C2059: syntax error: 'for'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/printing_impl.hpp(513): error C2143: syntax error: missing ')' before ';'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/printing_impl.hpp(513): error C2143: syntax error: missing ';' before '!='  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/printing_impl.hpp(513): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/printing_impl.hpp(513): error C2143: syntax error: missing ';' before '++'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/printing_impl.hpp(513): error C2086: 'int boost::parser::detail::text::detail::<begin>$L0': redefinition  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/printing_impl.hpp(513): note: see declaration of 'boost::parser::detail::text::detail::<begin>$L0'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/printing_impl.hpp(513): error C2059: syntax error: ')'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/printing_impl.hpp(513): error C2143: syntax error: missing ';' before '{'  
  
C:\Users\ContainerAdministrator\boost-root\boost/parser/detail/printing_impl.hpp(513): error C2447: '{': missing function header (old-style formal list?)  
  
libs\mysql\test\common\src\boost_mysql.cpp(9): fatal error C1903: unable to recover from previous error(s); stopping compilation  
  
  
    call "bin.v2\standalone\msvc\msvc-14.3\msvc-setup.bat"  >nul  
  
 cl /Zm800 -nologo "libs\mysql\test\common\src\boost_mysql.cpp" -c -Fo"bin.v2\libs\mysql\test\msvc-14.3\dbg\bst.m-ts-exctr-off\cxstd-17-iso\lnk-sttc\thrd-mlt\common\src\boost_mysql.obj"     -TP /wd4675 /EHs /std:c++17 /GR /Zc:throwingNew /Z7 /Od /Ob0 /W4 /MDd /Zc:forScope /Zc:wchar_t /Zc:inline /favor:blend /bigobj /wd4702 /permissive- /Zc:__cplusplus -DBOOST_ALLOW_DEPRECATED_HEADERS=1 -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_DISABLE_BOOST_ARRAY=1 -DBOOST_ASIO_DISABLE_BOOST_BIND=1 -DBOOST_ASIO_DISABLE_BOOST_COROUTINE=1 -DBOOST_ASIO_DISABLE_BOOST_DATE_TIME=1 -DBOOST_ASIO_DISABLE_BOOST_REGEX=1 -DBOOST_ASIO_HAS_DEFAULT_FUNCTION_TEMPLATE_ARGUMENTS=1 -DBOOST_ASIO_NO_DEPRECATED=1 -DBOOST_ASIO_SEPARATE_COMPILATION -DBOOST_MYSQL_SEPARATE_COMPILATION -D_SCL_SECURE_NO_WARNINGS=1 -D_SILENCE_CXX17_ADAPTOR_TYPEDEFS_DEPRECATION_WARNING -D_SILENCE_CXX17_ALLOCATOR_VOID_DEPRECATION_WARNING -D_SILENCE_CXX17_ITERATOR_BASE_CLASS_DEPRECATION_WARNING -D_WIN32_WINNT=0x0601 "-I." "-IC:\openssl-64\include" "-Ilibs\mysql\include"   
```  
  
I don't have right now the exact compiler version. Since this happens on both 14.2 and 14.3, you may not need it. If you happen to, I can get access to a Windows machine, run the build container and get it for you.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-03-30 18:49:18 UTC  
> Url: https://github.com/boostorg/parser/issues/132#issuecomment-2028440490  

That version of MSVC is not supported.  I only support VS2019 and later.  I would accept a patch that fixes this, but I'm not going to beat my head against MSVC.  The diagnostics are too awful outside of the IDE, and I only have access to VS2019 an VS2022.
