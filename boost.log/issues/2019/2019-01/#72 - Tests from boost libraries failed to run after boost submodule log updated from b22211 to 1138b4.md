# #72 - Tests from boost libraries failed to run after boost submodule log updated from b22211 to 1138b4 [Closed]

> Username: spacelg  
> Created at: 2019-01-16 05:51:31 UTC  
> Updated at: 2019-01-16 09:07:39 UTC  
> Closed at: 2019-01-16 09:07:39 UTC  
> Url: https://github.com/boostorg/log/issues/72  

Environment:  
VS 2017 + Windows Server 2016  
  
Issue description:  
We build and run test for boost and we found one tests failed to run after boost submodule log updated from b22211 to 1138b4 [boostorg/log@e575e40](https://github.com/boostorg/boost/commit/e575e40d555a2ab8fa0a27d12d0fd4a46a0a4d68)  
Could you please take a look?  
  
Reproduce steps:  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x86 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\Release --address-model=32  
5. .\b2 variant=release --build-dir=..\out\Release --address-model=32  
6. .\b2 -j4 variant=release --build-dir=..\out\x86rel libs\log\test  
  
[log_x86_test_60.log](https://github.com/boostorg/log/files/2762830/log_x86_test_60.log)  
  
ErrorMessage:  
..\out\x86rel\boost\bin.v2\libs\log\example\basic_usage\msvc-14.1\release\threadapi-win32\threading-multi\main.obj  
main.cpp  
.\boost/parameter/aux_/unwrap_cv_reference.hpp(34): error C2528: 'abstract declarator': pointer to reference is illegal  
.\boost/mpl/if.hpp(63): note: see reference to class template instantiation 'boost::parameter::aux::is_cv_reference_wrapper<T>' being compiled  
        with  
        [  
            T=const char (&)[11]  
        ]  
.\boost/mpl/eval_if.hpp(40): note: see reference to class template instantiation 'boost::mpl::if_<C,F1,F2>' being compiled  
        with  
        [  
            C=boost::parameter::aux::is_cv_reference_wrapper<const char (&)[11]>,  
            F1=boost::parameter::aux::get_type<const char (&)[11]>,  
            F2=boost::mpl::identity<const char (&)[11]>  
        ]  
.\boost/parameter/aux_/unwrap_cv_reference.hpp(84): note: see reference to class template instantiation 'boost::mpl::eval_if<boost::parameter::aux::is_cv_reference_wrapper<T>,boost::parameter::aux::get_type<T>,boost::mpl::identity<T>>' being compiled  
        with  
        [  
            T=const char (&)[11]  
        ]  
.\boost/parameter/aux_/tag.hpp(21): note: see reference to class template instantiation 'boost::parameter::aux::unwrap_cv_reference<ActualArg>' being compiled  
        with  
        [  
            ActualArg=const char (&)[11]  
        ]  
.\boost/log/utility/setup/file.hpp(127): note: see reference to class template instantiation 'boost::parameter::aux::tag<boost::log::v2_mt_nt6::keywords::tag::file_name,const char (&)[11]>' being compiled  
.\boost/log/utility/setup/file.hpp(107): note: see reference to function template instantiation 'parameter::aux::tag<boost::log::v2_mt_nt6::keywords::tag::file_name,const T&>::type boost::log::v2_mt_nt6::aux::wrap_file_name(const T &,boost::mpl::false_)' being compiled  
libs\log\example\basic_usage\main.cpp(103): note: see reference to function template instantiation 'boost::shared_ptr<boost::log::v2_mt_nt6::sinks::synchronous_sink<boost::log::v2_mt_nt6::sinks::text_file_backend>> boost::log::v2_mt_nt6::add_file_log<char[11],boost::parameter::aux::tagged_argument<Keyword,const boost::phoenix::actor<Expr>>,boost::parameter::aux::tagged_argument<boost::log::v2_mt_nt6::keywords::tag::format,const boost::phoenix::actor<boost::proto::exprns_::basic_expr<Tag,Args,2>>>>(const T0 (&),const T1 &,const T2 &)' being compiled  
        with  
        [  
            Keyword=boost::log::v2_mt_nt6::keywords::tag::filter,  
            Expr=boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::greater_equal,boost::proto::argsns_::list2<boost::log::v2_mt_nt6::expressions::attribute_actor<severity_level,boost::log::v2_mt_nt6::fallback_to_none,void,boost::phoenix::actor>,boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<severity_level>,0>>>,2>,  
            Tag=boost::proto::tagns_::tag::shift_left,  
            Args=boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left,boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2_mt_nt6::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left,boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2_mt_nt6::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left,boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2_mt_nt6::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left,boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2_mt_nt6::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<boost::phoenix::argument<2>>,0>>,boost::posix_time::ptime,boost::log::v2_mt_nt6::fallback_to_none,boost::log::v2_mt_nt6::aux::light_function<void (boost::log::v2_mt_nt6::basic_formatting_ostream<char,std::char_traits<char>,std::allocator<char>> &,const boost::posix_time::ptime &)>>>,boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<char [3]>,0>>>,2>>,boost::log::v2_mt_nt6::attributes::timer::value_type,boost::log::v2_mt_nt6::fallback_to_none,boost::log::v2_mt_nt6::aux::light_function<void (boost::log::v2_mt_nt6::basic_formatting_ostream<char,std::char_traits<char>,std::allocator<char>> &,const boost::posix_time::time_duration &)>>>,boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<char [4]>,0>>>,2>>,boost::log::v2_mt_nt6::attributes::named_scope::value_type,boost::log::v2_mt_nt6::fallback_to_none,boost::log::v2_mt_nt6::expressions::aux::format_named_scope_impl<char>>>,boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<char [4]>,0>>>,2>>,severity_level,boost::log::v2_mt_nt6::fallback_to_none,boost::log::v2_mt_nt6::to_log_fun<void>>>,boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<char [3]>,0>>>,2>>,boost::log::v2_mt_nt6::expressions::attribute_actor<boost::mpl::vector2<std::string,std::wstring>,boost::log::v2_mt_nt6::fallback_to_none,boost::log::v2_mt_nt6::expressions::tag::message,boost::phoenix::actor>>,  
            T0=char [11],  
            T1=boost::parameter::aux::tagged_argument<boost::log::v2_mt_nt6::keywords::tag::filter,const boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::greater_equal,boost::proto::argsns_::list2<boost::log::v2_mt_nt6::expressions::attribute_actor<severity_level,boost::log::v2_mt_nt6::fallback_to_none,void,boost::phoenix::actor>,boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<severity_level>,0>>>,2>>>,  
            T2=boost::parameter::aux::tagged_argument<boost::log::v2_mt_nt6::keywords::tag::format,const boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left,boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left,boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2_mt_nt6::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left,boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2_mt_nt6::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left,boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2_mt_nt6::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left,boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2_mt_nt6::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<boost::phoenix::argument<2>>,0>>,boost::posix_time::ptime,boost::log::v2_mt_nt6::fallback_to_none,boost::log::v2_mt_nt6::aux::light_function<void (boost::log::v2_mt_nt6::basic_formatting_ostream<char,std::char_traits<char>,std::allocator<char>> &,const boost::posix_time::ptime &)>>>,boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<char [3]>,0>>>,2>>,boost::log::v2_mt_nt6::attributes::timer::value_type,boost::log::v2_mt_nt6::fallback_to_none,boost::log::v2_mt_nt6::aux::light_function<void (boost::log::v2_mt_nt6::basic_formatting_ostream<char,std::char_traits<char>,std::allocator<char>> &,const boost::posix_time::time_duration &)>>>,boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<char [4]>,0>>>,2>>,boost::log::v2_mt_nt6::attributes::named_scope::value_type,boost::log::v2_mt_nt6::fallback_to_none,boost::log::v2_mt_nt6::expressions::aux::format_named_scope_impl<char>>>,boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<char [4]>,0>>>,2>>,severity_level,boost::log::v2_mt_nt6::fallback_to_none,boost::log::v2_mt_nt6::to_log_fun<void>>>,boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<char [3]>,0>>>,2>>,boost::log::v2_mt_nt6::expressions::attribute_actor<boost::mpl::vector2<std::string,std::wstring>,boost::log::v2_mt_nt6::fallback_to_none,boost::log::v2_mt_nt6::expressions::tag::message,boost::phoenix::actor>>,2>>>  
        ]  
.\boost/parameter/aux_/unwrap_cv_reference.hpp(34): error C2528: '<template-parameter>': pointer to reference is illegal  
  
    call "..\out\x86rel\boost\bin.v2\standalone\msvc\msvc-14.1\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo @"..\out\x86rel\boost\bin.v2\libs\log\example\basic_usage\msvc-14.1\release\threadapi-win32\threading-multi\main.obj.rsp"

---

## Comment 1

> Username: Lastique  
> Created at: 2019-01-16 09:07:39 UTC  
> Url: https://github.com/boostorg/log/issues/72#issuecomment-454704543  

See https://github.com/boostorg/log/pull/68#issuecomment-454600804.
