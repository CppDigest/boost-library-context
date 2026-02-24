# #5 - 2 issues when build with msvc on windows [Closed]

> Username: PhoebeHui  
> Created at: 2018-10-09 05:52:59 UTC  
> Updated at: 2019-04-05 22:42:55 UTC  
> Closed at: 2019-04-05 22:42:55 UTC  
> Url: https://github.com/boostorg/proto/issues/5  

Hi,  
We tried to build and run proto test for Boost with visual studio latest release on Windows. It failed with 2 source issues, Could you please help take a look at this? Thank you!  
Reproduce steps:  
1.	git clone -c core.autocrlf=true --recursive https://github.com/boostorg/boost.git D:\Boost\src  
2.	open a VS 2017 x86 command prompt and browse to D:\Boost\src  
3.	.\bootstrap  
4.	.\b2 headers variant=release --build-dir=..\out\x86rel address-model=32  
5.	.\b2 variant=release --build-dir=..\out\x86rel address-model=32  
6.	.\b2 -j4 variant=debug --build-dir=..\out\x86dbg libs\proto\test  
Failures:  
Issue#1  
display_expr.obj : error LNK2019: unresolved external symbol "private: __thiscall boost::proto::detail::display_expr_impl::display_expr_impl(struct boost::proto::detail::display_expr_impl const &)" (??0display_expr_impl@detail@proto@boost@@AAE@ABU0123@@Z) referenced in function "private: void __thiscall boost::proto::detail::display_expr_impl::impl<struct boost::proto::exprns_::expr<struct boost::proto::tagns_::tag::plus,struct boost::proto::argsns_::list2<struct boost::proto::exprns_::expr<struct boost::proto::tagns_::tag::terminal,struct boost::proto::argsns_::term<int>,0> &,struct boost::proto::exprns_::expr<struct boost::proto::tagns_::tag::terminal,struct boost::proto::argsns_::term<struct A const &>,0> >,2>,struct boost::mpl::long_<2> >(struct boost::proto::exprns_::expr<struct boost::proto::tagns_::tag::plus,struct boost::proto::argsns_::list2<struct boost::proto::exprns_::expr<struct boost::proto::tagns_::tag::terminal,struct boost::proto::argsns_::term<int>,0> &,struct boost::proto::exprns_::expr<struct boost::proto::tagns_::tag::terminal,struct boost::proto::argsns_::term<struct A const &>,0> >,2> const &,struct boost::mpl::long_<2>)const " (??$impl@U?$expr@Uplus@tag@tagns_@proto@boost@@U?$list2@AAU?$expr@Uterminal@tag@tagns_@proto@boost@@U?$term@H@argsns_@45@$0A@@exprns_@proto@boost@@U?$expr@Uterminal@tag@tagns_@proto@boost@@U?$term@ABUA@@@argsns_@45@$0A@@234@@argsns_@45@$01@exprns_@proto@boost@@U?$long_@$01@mpl@4@@display_expr_impl@detail@proto@boost@@ABEXABU?$expr@Uplus@tag@tagns_@proto@boost@@U?$list2@AAU?$expr@Uterminal@tag@tagns_@proto@boost@@U?$term@H@argsns_@45@$0A@@exprns_@proto@boost@@U?$expr@Uterminal@tag@tagns_@proto@boost@@U?$term@ABUA@@@argsns_@45@$0A@@234@@argsns_@45@$01@exprns_@23@U?$long_@$01@mpl@3@@Z)  
..\out\x86dbg\boost\bin.v2\libs\proto\test\display_expr.test\msvc-14.1\debug\link-static\threading-multi\display_expr.exe : fatal error LNK1120: 1 unresolved externals  
  
Issue#2  
examples.cpp  
D:\Boost\src\boost/mpl/aux_/preprocessed/plain/less.hpp(60): error C2039: 'tag': is not a member of 'lambda_transform::placeholder<boost::mpl::int_<1>>'  
libs\proto\test\examples.cpp(405): note: see declaration of 'lambda_transform::placeholder<boost::mpl::int_<1>>'  
D:\Boost\src\boost/mpl/aux_/preprocessed/plain/less.hpp(69): note: see reference to class template instantiation 'boost::mpl::less_tag<N1>' being compiled  
        with  
        [  
            N1=const lambda_transform::placeholder<boost::mpl::int_<1>>  
        ]  
D:\Boost\src\boost/mpl/aux_/preprocessed/plain/or.hpp(50): note: see reference to class template instantiation 'boost::mpl::less<N,boost::mpl::int_<2>>' being compiled  
        with  
        [  
            N=const lambda_transform::placeholder<boost::mpl::int_<1>>  
        ]  
D:\Boost\src\boost/mpl/if.hpp(63): note: see reference to class template instantiation 'boost::mpl::or_<boost::mpl::less<N,boost::mpl::int_<2>>,boost::fusion::traits::is_unbounded<Sequence>,boost::mpl::false_,boost::mpl::false_,boost::mpl::false_>' being compiled  
        with  
        [  
            N=const lambda_transform::placeholder<boost::mpl::int_<1>>,  
            Sequence=boost::fusion::tuple<int,int>  
        ]  
D:\Boost\src\boost/fusion/sequence/intrinsic/at.hpp(67): note: see reference to class template instantiation 'boost::mpl::if_<boost::mpl::or_<boost::mpl::less<N,boost::mpl::int_<2>>,boost::fusion::traits::is_unbounded<Sequence>,boost::mpl::false_,boost::mpl::false_,boost::mpl::false_>,boost::fusion::extension::at_impl<boost::fusion::vector_tag>::apply<Sequence,N>,boost::mpl::empty_base>' being compiled  
        with  
        [  
            N=const lambda_transform::placeholder<boost::mpl::int_<1>>,  
            Sequence=boost::fusion::tuple<int,int>  
        ]  
D:\Boost\src\boost/fusion/sequence/intrinsic/at.hpp(83): note: see reference to class template instantiation 'boost::fusion::detail::at_impl<Sequence,N,boost::fusion::vector_tag>' being compiled  
        with  
        [  
            Sequence=boost::fusion::tuple<int,int>,  
            N=const lambda_transform::placeholder<boost::mpl::int_<1>>  
        ]  
libs\proto\test\examples.cpp(376): note: see reference to class template instantiation 'boost::fusion::result_of::at<boost::fusion::tuple<int,int>,const lambda_transform::placeholder<boost::mpl::int_<1>>>' being compiled  
D:\Boost\src\boost/proto/detail/poly_function.hpp(205): note: see reference to class template instantiation 'lambda_transform::at::result<Sig>' being compiled  
        with  
        [  
            Sig=lambda_transform::at (boost::fusion::tuple<int,int> &,const lambda_transform::placeholder<boost::mpl::int_<1>> &)  
        ]  
D:\Boost\src\boost/proto/transform/call.hpp(246): note: see reference to class template instantiation 'boost::proto::detail::poly_function_traits<Fun,Fun (boost::fusion::tuple<int,int> &,const lambda_transform::placeholder<boost::mpl::int_<1>> &),boost::mpl::size_t<3>>' being compiled  
        with  
        [  
            Fun=lambda_transform::at  
        ]  
D:\Boost\src\boost/proto/transform/call.hpp(313): note: see reference to class template instantiation 'boost::proto::call<Fun>::impl2<Expr,State,Data,false>' being compiled  
        with  
        [  
            Fun=lambda_transform::at (boost::proto::_data,boost::proto::_value),  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<1>>>,0> &,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
D:\Boost\src\boost/proto/transform/when.hpp(59): note: see reference to class template instantiation 'boost::proto::call<Fun>::impl<Expr,State,Data>' being compiled  
        with  
        [  
            Fun=lambda_transform::at (boost::proto::_data,boost::proto::_value),  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<1>>>,0> &,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
D:\Boost\src\boost/proto/matches.hpp(762): note: see reference to class template instantiation 'boost::proto::detail::when_impl<Grammar,R,R (A0,A1)>::impl<Expr &,State,Data>' being compiled  
        with  
        [  
            Grammar=boost::proto::terminal<lambda_transform::placeholder<boost::proto::_>>,  
            R=lambda_transform::at,  
            A0=boost::proto::_data,  
            A1=boost::proto::_value,  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<1>>>,0>,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
D:\Boost\src\boost/proto/transform/default.hpp(141): note: see reference to class template instantiation 'boost::proto::or_<boost::proto::when<boost::proto::terminal<lambda_transform::placeholder<boost::proto::_>>,lambda_transform::at (boost::proto::_data,boost::proto::_value)>,boost::proto::otherwise<boost::proto::_default<lambda_transform::LambdaEval>>,void,void,void,void,void,void,void,void>::impl<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<T>,0> &,State,Data>' being compiled  
        with  
        [  
            T=lambda_transform::placeholder<boost::mpl::int_<1>>,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
D:\Boost\src\boost/proto/matches.hpp(836): note: see reference to class template instantiation 'boost::proto::detail::default_minus<Grammar>::impl<Expr &,State,Data>' being compiled  
        with  
        [  
            Grammar=lambda_transform::LambdaEval,  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::minus,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<1>>>,0> &,const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0> &>,2>,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
D:\Boost\src\boost/proto/matches.hpp(762): note: see reference to class template instantiation 'boost::proto::switch_<boost::proto::detail::default_cases<Grammar>,boost::proto::tag_of<boost::proto::_> (void)>::impl<Expr &,State,Data>' being compiled  
        with  
        [  
            Grammar=lambda_transform::LambdaEval,  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::minus,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<1>>>,0> &,const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0> &>,2>,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
D:\Boost\src\boost/proto/transform/impl.hpp(249): note: see reference to class template instantiation 'boost::proto::or_<boost::proto::when<boost::proto::terminal<lambda_transform::placeholder<boost::proto::_>>,lambda_transform::at (boost::proto::_data,boost::proto::_value)>,boost::proto::otherwise<boost::proto::_default<lambda_transform::LambdaEval>>,void,void,void,void,void,void,void,void>::impl<Expr,State,Data>' being compiled  
        with  
        [  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::minus,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<1>>>,0> &,const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0> &>,2> &,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
libs\proto\test\examples.cpp(414): note: see reference to class template instantiation 'boost::proto::detail::apply_transform<boost::proto::or_<boost::proto::when<boost::proto::terminal<lambda_transform::placeholder<boost::proto::_>>,lambda_transform::at (boost::proto::_data,boost::proto::_value)>,boost::proto::otherwise<boost::proto::_default<lambda_transform::LambdaEval>>,void,void,void,void,void,void,void,void> (const boost::proto::exprns_::expr<Tag,Args,2> &,const int &,boost::fusion::tuple<int,int> &)>' being compiled  
        with  
        [  
            Tag=boost::proto::tagns_::tag::minus,  
            Args=boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<1>>>,0> &,const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0> &>  
        ]  
D:\Boost\src\boost/proto/transform/impl.hpp(255): note: while compiling class template member function 'boost::proto::detail::apply_transform<boost::proto::or_<boost::proto::when<boost::proto::terminal<lambda_transform::placeholder<boost::proto::_>>,lambda_transform::at (boost::proto::_data,boost::proto::_value)>,boost::proto::otherwise<boost::proto::_default<lambda_transform::LambdaEval>>,void,void,void,void,void,void,void,void>(const Expr &,const State &,const Data &)>::result_type boost::proto::transform<boost::proto::or_<boost::proto::when<boost::proto::terminal<lambda_transform::placeholder<boost::proto::_>>,lambda_transform::at (boost::proto::_data,boost::proto::_value)>,boost::proto::otherwise<boost::proto::_default<lambda_transform::LambdaEval>>,void,void,void,void,void,void,void,void>,void>::operator ()(Expr &&,State &&,Data &&) const'  
D:\Boost\src\boost/mpl/aux_/preprocessed/plain/less.hpp(69): error C3203: 'tag': unspecialized class template can't be used as a template argument for template parameter 'Tag1', expected a real type  
D:\Boost\src\boost/mpl/aux_/preprocessed/plain/less.hpp(19): error C2825: 'Tag1': must be a class or namespace when followed by '::'  
D:\Boost\src\boost/mpl/aux_/preprocessed/plain/less.hpp(69): note: see reference to class template instantiation 'boost::mpl::less_impl<int,boost::mpl::integral_c_tag>' being compiled  
D:\Boost\src\boost/mpl/aux_/preprocessed/plain/less.hpp(20): error C2510: 'Tag1': left of '::' must be a class/struct/union  
D:\Boost\src\boost/mpl/aux_/preprocessed/plain/less.hpp(19): error C2065: 'value': undeclared identifier  
D:\Boost\src\boost/mpl/aux_/preprocessed/plain/apply_wrap.hpp(39): error C2504: 'boost::mpl::numeric_cast<Tag1,Tag2>::apply<T1>': base class undefined  
        with  
        [  
            Tag1=int,  
            Tag2=boost::mpl::integral_c_tag  
        ]  
        and  
        [  
            T1=const lambda_transform::placeholder<boost::mpl::int_<1>>  
        ]  
D:\Boost\src\boost/mpl/aux_/numeric_cast_utils.hpp(34): note: see reference to class template instantiation 'boost::mpl::apply_wrap1<boost::mpl::numeric_cast<Tag1,Tag2>,N1>' being compiled  
        with  
        [  
            Tag1=int,  
            Tag2=boost::mpl::integral_c_tag,  
            N1=const lambda_transform::placeholder<boost::mpl::int_<1>>  
        ]  
D:\Boost\src\boost/mpl/aux_/preprocessed/plain/less.hpp(69): note: see reference to class template instantiation 'boost::mpl::aux::cast1st_impl<boost::mpl::less_impl<boost::mpl::integral_c_tag,boost::mpl::integral_c_tag>,Tag1,Tag2>::apply<N1,N2>' being compiled  
        with  
        [  
            Tag1=int,  
            Tag2=boost::mpl::integral_c_tag,  
            N1=const lambda_transform::placeholder<boost::mpl::int_<1>>,  
            N2=boost::mpl::int_<2>  
        ]  
D:\Boost\src\boost/mpl/aux_/numeric_cast_utils.hpp(34): error C2039: 'type': is not a member of 'boost::mpl::apply_wrap1<boost::mpl::numeric_cast<Tag1,Tag2>,N1>'  
        with  
        [  
            Tag1=int,  
            Tag2=boost::mpl::integral_c_tag,  
            N1=const lambda_transform::placeholder<boost::mpl::int_<1>>  
        ]  
D:\Boost\src\boost/mpl/aux_/numeric_cast_utils.hpp(34): note: see declaration of 'boost::mpl::apply_wrap1<boost::mpl::numeric_cast<Tag1,Tag2>,N1>'  
        with  
        [  
            Tag1=int,  
            Tag2=boost::mpl::integral_c_tag,  
            N1=const lambda_transform::placeholder<boost::mpl::int_<1>>  
        ]  
D:\Boost\src\boost/mpl/aux_/numeric_cast_utils.hpp(36): error C3203: 'type': unspecialized class template can't be used as a template argument for template parameter 'T1', expected a real type  
D:\Boost\src\boost/mpl/aux_/preprocessed/plain/less.hpp(87): error C2825: 'N1': must be a class or namespace when followed by '::'  
D:\Boost\src\boost/mpl/aux_/preprocessed/plain/apply_wrap.hpp(49): note: see reference to class template instantiation 'boost::mpl::less_impl<boost::mpl::integral_c_tag,boost::mpl::integral_c_tag>::apply<T1,T2>' being compiled  
        with  
        [  
            T1=int,  
            T2=boost::mpl::int_<2>  
        ]  
D:\Boost\src\boost/mpl/aux_/numeric_cast_utils.hpp(37): note: see reference to class template instantiation 'boost::mpl::apply_wrap2<F,int,N2>' being compiled  
        with  
        [  
            F=boost::mpl::less_impl<boost::mpl::integral_c_tag,boost::mpl::integral_c_tag>,  
            N2=boost::mpl::int_<2>  
        ]  
D:\Boost\src\boost/mpl/aux_/preprocessed/plain/less.hpp(87): error C2510: 'N1': left of '::' must be a class/struct/union  
D:\Boost\src\boost/mpl/aux_/preprocessed/plain/less.hpp(87): error C2065: 'value': undeclared identifier  
D:\Boost\src\boost/proto/detail/poly_function.hpp(205): error C2039: 'type': is not a member of 'lambda_transform::at::result<Sig>'  
        with  
        [  
            Sig=lambda_transform::at (boost::fusion::tuple<int,int> &,const lambda_transform::placeholder<boost::mpl::int_<1>> &)  
        ]  
D:\Boost\src\boost/proto/detail/poly_function.hpp(205): note: see declaration of 'lambda_transform::at::result<Sig>'  
        with  
        [  
            Sig=lambda_transform::at (boost::fusion::tuple<int,int> &,const lambda_transform::placeholder<boost::mpl::int_<1>> &)  
        ]  
D:\Boost\src\boost/mpl/aux_/preprocessed/plain/less.hpp(60): error C2039: 'tag': is not a member of 'lambda_transform::placeholder<boost::mpl::int_<0>>'  
libs\proto\test\examples.cpp(404): note: see declaration of 'lambda_transform::placeholder<boost::mpl::int_<0>>'  
D:\Boost\src\boost/mpl/aux_/preprocessed/plain/less.hpp(69): note: see reference to class template instantiation 'boost::mpl::less_tag<N1>' being compiled  
        with  
        [  
            N1=const lambda_transform::placeholder<boost::mpl::int_<0>>  
        ]  
D:\Boost\src\boost/mpl/aux_/preprocessed/plain/or.hpp(50): note: see reference to class template instantiation 'boost::mpl::less<N,boost::mpl::int_<2>>' being compiled  
        with  
        [  
            N=const lambda_transform::placeholder<boost::mpl::int_<0>>  
        ]  
D:\Boost\src\boost/mpl/if.hpp(63): note: see reference to class template instantiation 'boost::mpl::or_<boost::mpl::less<N,boost::mpl::int_<2>>,boost::fusion::traits::is_unbounded<Sequence>,boost::mpl::false_,boost::mpl::false_,boost::mpl::false_>' being compiled  
        with  
        [  
            N=const lambda_transform::placeholder<boost::mpl::int_<0>>,  
            Sequence=boost::fusion::tuple<int,int>  
        ]  
D:\Boost\src\boost/fusion/sequence/intrinsic/at.hpp(67): note: see reference to class template instantiation 'boost::mpl::if_<boost::mpl::or_<boost::mpl::less<N,boost::mpl::int_<2>>,boost::fusion::traits::is_unbounded<Sequence>,boost::mpl::false_,boost::mpl::false_,boost::mpl::false_>,boost::fusion::extension::at_impl<boost::fusion::vector_tag>::apply<Sequence,N>,boost::mpl::empty_base>' being compiled  
        with  
        [  
            N=const lambda_transform::placeholder<boost::mpl::int_<0>>,  
            Sequence=boost::fusion::tuple<int,int>  
        ]  
D:\Boost\src\boost/fusion/sequence/intrinsic/at.hpp(83): note: see reference to class template instantiation 'boost::fusion::detail::at_impl<Sequence,N,boost::fusion::vector_tag>' being compiled  
        with  
        [  
            Sequence=boost::fusion::tuple<int,int>,  
            N=const lambda_transform::placeholder<boost::mpl::int_<0>>  
        ]  
libs\proto\test\examples.cpp(376): note: see reference to class template instantiation 'boost::fusion::result_of::at<boost::fusion::tuple<int,int>,const lambda_transform::placeholder<boost::mpl::int_<0>>>' being compiled  
D:\Boost\src\boost/proto/detail/poly_function.hpp(205): note: see reference to class template instantiation 'lambda_transform::at::result<Sig>' being compiled  
        with  
        [  
            Sig=lambda_transform::at (boost::fusion::tuple<int,int> &,const lambda_transform::placeholder<boost::mpl::int_<0>> &)  
        ]  
D:\Boost\src\boost/proto/transform/call.hpp(246): note: see reference to class template instantiation 'boost::proto::detail::poly_function_traits<Fun,Fun (boost::fusion::tuple<int,int> &,const lambda_transform::placeholder<boost::mpl::int_<0>> &),boost::mpl::size_t<3>>' being compiled  
        with  
        [  
            Fun=lambda_transform::at  
        ]  
D:\Boost\src\boost/proto/transform/call.hpp(313): note: see reference to class template instantiation 'boost::proto::call<Fun>::impl2<Expr,State,Data,false>' being compiled  
        with  
        [  
            Fun=lambda_transform::at (boost::proto::_data,boost::proto::_value),  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0> &,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
D:\Boost\src\boost/proto/transform/when.hpp(59): note: see reference to class template instantiation 'boost::proto::call<Fun>::impl<Expr,State,Data>' being compiled  
        with  
        [  
            Fun=lambda_transform::at (boost::proto::_data,boost::proto::_value),  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0> &,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
D:\Boost\src\boost/proto/matches.hpp(762): note: see reference to class template instantiation 'boost::proto::detail::when_impl<Grammar,R,R (A0,A1)>::impl<Expr &,State,Data>' being compiled  
        with  
        [  
            Grammar=boost::proto::terminal<lambda_transform::placeholder<boost::proto::_>>,  
            R=lambda_transform::at,  
            A0=boost::proto::_data,  
            A1=boost::proto::_value,  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0>,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
D:\Boost\src\boost/proto/transform/default.hpp(141): note: see reference to class template instantiation 'boost::proto::or_<boost::proto::when<boost::proto::terminal<lambda_transform::placeholder<boost::proto::_>>,lambda_transform::at (boost::proto::_data,boost::proto::_value)>,boost::proto::otherwise<boost::proto::_default<lambda_transform::LambdaEval>>,void,void,void,void,void,void,void,void>::impl<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<T>,0> &,State,Data>' being compiled  
        with  
        [  
            T=lambda_transform::placeholder<boost::mpl::int_<0>>,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
D:\Boost\src\boost/mpl/aux_/preprocessed/plain/apply_wrap.hpp(39): error C2504: 'boost::mpl::numeric_cast<Tag1,Tag2>::apply<T1>': base class undefined  
        with  
        [  
            Tag1=int,  
            Tag2=boost::mpl::integral_c_tag  
        ]  
        and  
        [  
            T1=const lambda_transform::placeholder<boost::mpl::int_<0>>  
        ]  
D:\Boost\src\boost/mpl/aux_/numeric_cast_utils.hpp(34): note: see reference to class template instantiation 'boost::mpl::apply_wrap1<boost::mpl::numeric_cast<Tag1,Tag2>,N1>' being compiled  
        with  
        [  
            Tag1=int,  
            Tag2=boost::mpl::integral_c_tag,  
            N1=const lambda_transform::placeholder<boost::mpl::int_<0>>  
        ]  
D:\Boost\src\boost/mpl/aux_/preprocessed/plain/less.hpp(69): note: see reference to class template instantiation 'boost::mpl::aux::cast1st_impl<boost::mpl::less_impl<boost::mpl::integral_c_tag,boost::mpl::integral_c_tag>,Tag1,Tag2>::apply<N1,N2>' being compiled  
        with  
        [  
            Tag1=int,  
            Tag2=boost::mpl::integral_c_tag,  
            N1=const lambda_transform::placeholder<boost::mpl::int_<0>>,  
            N2=boost::mpl::int_<2>  
        ]  
D:\Boost\src\boost/mpl/aux_/numeric_cast_utils.hpp(34): error C2039: 'type': is not a member of 'boost::mpl::apply_wrap1<boost::mpl::numeric_cast<Tag1,Tag2>,N1>'  
        with  
        [  
            Tag1=int,  
            Tag2=boost::mpl::integral_c_tag,  
            N1=const lambda_transform::placeholder<boost::mpl::int_<0>>  
        ]  
D:\Boost\src\boost/mpl/aux_/numeric_cast_utils.hpp(34): note: see declaration of 'boost::mpl::apply_wrap1<boost::mpl::numeric_cast<Tag1,Tag2>,N1>'  
        with  
        [  
            Tag1=int,  
            Tag2=boost::mpl::integral_c_tag,  
            N1=const lambda_transform::placeholder<boost::mpl::int_<0>>  
        ]  
D:\Boost\src\boost/proto/detail/poly_function.hpp(205): error C2039: 'type': is not a member of 'lambda_transform::at::result<Sig>'  
        with  
        [  
            Sig=lambda_transform::at (boost::fusion::tuple<int,int> &,const lambda_transform::placeholder<boost::mpl::int_<0>> &)  
        ]  
D:\Boost\src\boost/proto/detail/poly_function.hpp(205): note: see declaration of 'lambda_transform::at::result<Sig>'  
        with  
        [  
            Sig=lambda_transform::at (boost::fusion::tuple<int,int> &,const lambda_transform::placeholder<boost::mpl::int_<0>> &)  
        ]  
D:\Boost\src\boost/proto/transform/default.hpp(141): error C2672: 'boost::proto::detail::make': no matching overloaded function found  
D:\Boost\src\boost/proto/transform/default.hpp(141): error C3206: 'boost::proto::detail::make': invalid template argument for 'T', missing template argument list on class template 'boost::type'  
D:\Boost\src\boost/proto/detail/decltype.hpp(89): note: see declaration of 'boost::proto::detail::make'  
D:\Boost\src\boost/proto/transform/default.hpp(141): error C2672: 'boost::proto::detail::check_reference': no matching overloaded function found  
D:\Boost\src\boost/proto/transform/default.hpp(141): error C3206: 'boost::proto::detail::make': invalid template argument for 'T', missing template argument list on class template 'boost::type'  
D:\Boost\src\boost/proto/detail/decltype.hpp(89): note: see declaration of 'boost::proto::detail::make'  
D:\Boost\src\boost/proto/transform/default.hpp(141): error C2062: type 'unknown-type' unexpected  
D:\Boost\src\boost/proto/transform/default.hpp(141): error C2238: unexpected token(s) preceding ';'  
D:\Boost\src\boost/proto/transform/default.hpp(141): error C2039: 'type': is not a member of 'boost::proto::detail::default_minus<Grammar>::impl<Expr &,State,Data>::nested_and_hidden_nested_result_type'  
        with  
        [  
            Grammar=lambda_transform::LambdaEval,  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::minus,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<1>>>,0> &,const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0> &>,2>,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
D:\Boost\src\boost/proto/transform/default.hpp(141): note: see declaration of 'boost::proto::detail::default_minus<Grammar>::impl<Expr &,State,Data>::nested_and_hidden_nested_result_type'  
        with  
        [  
            Grammar=lambda_transform::LambdaEval,  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::minus,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<1>>>,0> &,const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0> &>,2>,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
D:\Boost\src\boost/proto/transform/default.hpp(141): note: see reference to class template instantiation 'boost::proto::detail::default_minus<Grammar>::impl<Expr &,State,Data>::nested_result_type' being compiled  
        with  
        [  
            Grammar=lambda_transform::LambdaEval,  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::minus,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<1>>>,0> &,const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0> &>,2>,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
D:\Boost\src\boost/proto/transform/default.hpp(141): error C2955: 'boost::type': use of class template requires template argument list  
D:\Boost\src\boost/type.hpp(14): note: see declaration of 'boost::type'  
D:\Boost\src\boost/proto/transform/default.hpp(141): error C2955: 'boost::mpl::if_c': use of class template requires template argument list  
D:\Boost\src\boost/mpl/if.hpp(35): note: see declaration of 'boost::mpl::if_c'  
D:\Boost\src\boost/proto/transform/default.hpp(141): error C2039: 'type': is not a member of 'boost::proto::detail::default_minus<Grammar>::impl<Expr &,State,Data>::nested_result_type'  
        with  
        [  
            Grammar=lambda_transform::LambdaEval,  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::minus,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<1>>>,0> &,const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0> &>,2>,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
D:\Boost\src\boost/proto/transform/default.hpp(141): note: see declaration of 'boost::proto::detail::default_minus<Grammar>::impl<Expr &,State,Data>::nested_result_type'  
        with  
        [  
            Grammar=lambda_transform::LambdaEval,  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::minus,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<1>>>,0> &,const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0> &>,2>,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
libs\proto\test\examples.cpp(414): error C2440: 'initializing': cannot convert from 'boost::type' to 'int'  
libs\proto\test\examples.cpp(414): note: No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
D:\Boost\src\boost/proto/transform/default.hpp(160): error C2672: 'boost::proto::detail::make_mutable': no matching overloaded function found  
D:\Boost\src\boost/proto/matches.hpp(836): note: see reference to class template instantiation 'boost::proto::detail::default_plus_assign<Grammar>::impl<Expr &,State,Data>' being compiled  
        with  
        [  
            Grammar=lambda_transform::LambdaEval,  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::plus_assign,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0> &,boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const int &>,0>>,2>,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
D:\Boost\src\boost/proto/matches.hpp(762): note: see reference to class template instantiation 'boost::proto::switch_<boost::proto::detail::default_cases<Grammar>,boost::proto::tag_of<boost::proto::_> (void)>::impl<Expr &,State,Data>' being compiled  
        with  
        [  
            Grammar=lambda_transform::LambdaEval,  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::plus_assign,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0> &,boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const int &>,0>>,2>,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
D:\Boost\src\boost/proto/transform/impl.hpp(249): note: see reference to class template instantiation 'boost::proto::or_<boost::proto::when<boost::proto::terminal<lambda_transform::placeholder<boost::proto::_>>,lambda_transform::at (boost::proto::_data,boost::proto::_value)>,boost::proto::otherwise<boost::proto::_default<lambda_transform::LambdaEval>>,void,void,void,void,void,void,void,void>::impl<Expr,State,Data>' being compiled  
        with  
        [  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::plus_assign,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0> &,boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const int &>,0>>,2> &,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
libs\proto\test\examples.cpp(424): note: see reference to class template instantiation 'boost::proto::detail::apply_transform<boost::proto::or_<boost::proto::when<boost::proto::terminal<lambda_transform::placeholder<boost::proto::_>>,lambda_transform::at (boost::proto::_data,boost::proto::_value)>,boost::proto::otherwise<boost::proto::_default<lambda_transform::LambdaEval>>,void,void,void,void,void,void,void,void> (const boost::proto::exprns_::expr<Tag,Args,2> &,const int &,boost::fusion::tuple<int,int> &)>' being compiled  
        with  
        [  
            Tag=boost::proto::tagns_::tag::plus_assign,  
            Args=boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0> &,boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const int &>,0>>  
        ]  
D:\Boost\src\boost/proto/transform/impl.hpp(255): note: while compiling class template member function 'boost::proto::detail::apply_transform<boost::proto::or_<boost::proto::when<boost::proto::terminal<lambda_transform::placeholder<boost::proto::_>>,lambda_transform::at (boost::proto::_data,boost::proto::_value)>,boost::proto::otherwise<boost::proto::_default<lambda_transform::LambdaEval>>,void,void,void,void,void,void,void,void>(const Expr &,const State &,const Data &)>::result_type boost::proto::transform<boost::proto::or_<boost::proto::when<boost::proto::terminal<lambda_transform::placeholder<boost::proto::_>>,lambda_transform::at (boost::proto::_data,boost::proto::_value)>,boost::proto::otherwise<boost::proto::_default<lambda_transform::LambdaEval>>,void,void,void,void,void,void,void,void>,void>::operator ()(Expr &&,State &&,Data &&) const'  
D:\Boost\src\boost/proto/transform/default.hpp(160): error C3206: 'boost::proto::detail::make_mutable': invalid template argument for 'T', missing template argument list on class template 'boost::type'  
D:\Boost\src\boost/proto/detail/decltype.hpp(93): note: see declaration of 'boost::proto::detail::make_mutable'  
D:\Boost\src\boost/proto/transform/default.hpp(160): error C2672: 'boost::proto::detail::check_reference': no matching overloaded function found  
D:\Boost\src\boost/proto/transform/default.hpp(160): error C3206: 'boost::proto::detail::make_mutable': invalid template argument for 'T', missing template argument list on class template 'boost::type'  
D:\Boost\src\boost/proto/detail/decltype.hpp(93): note: see declaration of 'boost::proto::detail::make_mutable'  
D:\Boost\src\boost/proto/transform/default.hpp(160): error C2062: type 'unknown-type' unexpected  
D:\Boost\src\boost/proto/transform/default.hpp(160): error C2238: unexpected token(s) preceding ';'  
D:\Boost\src\boost/proto/transform/default.hpp(160): error C2039: 'type': is not a member of 'boost::proto::detail::default_plus_assign<Grammar>::impl<Expr &,State,Data>::nested_and_hidden_nested_result_type'  
        with  
        [  
            Grammar=lambda_transform::LambdaEval,  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::plus_assign,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0> &,boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const int &>,0>>,2>,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
D:\Boost\src\boost/proto/transform/default.hpp(160): note: see declaration of 'boost::proto::detail::default_plus_assign<Grammar>::impl<Expr &,State,Data>::nested_and_hidden_nested_result_type'  
        with  
        [  
            Grammar=lambda_transform::LambdaEval,  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::plus_assign,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0> &,boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const int &>,0>>,2>,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
D:\Boost\src\boost/proto/transform/default.hpp(160): note: see reference to class template instantiation 'boost::proto::detail::default_plus_assign<Grammar>::impl<Expr &,State,Data>::nested_result_type' being compiled  
        with  
        [  
            Grammar=lambda_transform::LambdaEval,  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::plus_assign,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0> &,boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const int &>,0>>,2>,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
D:\Boost\src\boost/proto/transform/default.hpp(160): error C2955: 'boost::type': use of class template requires template argument list  
D:\Boost\src\boost/type.hpp(14): note: see declaration of 'boost::type'  
D:\Boost\src\boost/proto/transform/default.hpp(160): error C2955: 'boost::mpl::if_c': use of class template requires template argument list  
D:\Boost\src\boost/mpl/if.hpp(35): note: see declaration of 'boost::mpl::if_c'  
D:\Boost\src\boost/proto/transform/default.hpp(160): error C2039: 'type': is not a member of 'boost::proto::detail::default_plus_assign<Grammar>::impl<Expr &,State,Data>::nested_result_type'  
        with  
        [  
            Grammar=lambda_transform::LambdaEval,  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::plus_assign,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0> &,boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const int &>,0>>,2>,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]  
D:\Boost\src\boost/proto/transform/default.hpp(160): note: see declaration of 'boost::proto::detail::default_plus_assign<Grammar>::impl<Expr &,State,Data>::nested_result_type'  
        with  
        [  
            Grammar=lambda_transform::LambdaEval,  
            Expr=const boost::proto::exprns_::expr<boost::proto::tagns_::tag::plus_assign,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<lambda_transform::placeholder<boost::mpl::int_<0>>>,0> &,boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const int &>,0>>,2>,  
            State=const int &,  
            Data=boost::fusion::tuple<int,int> &  
        ]
