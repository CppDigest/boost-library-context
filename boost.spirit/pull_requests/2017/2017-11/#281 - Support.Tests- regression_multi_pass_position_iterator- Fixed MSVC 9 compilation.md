# #281 Support.Tests: regression_multi_pass_position_iterator: Fixed MSVC 9 compilation [Merged]

> Username: Kojoley  
> Created at: 2017-11-15 23:52:09 UTC  
> Updated at: 2017-11-15 23:52:19 UTC  
> Merged at: 2017-11-15 23:52:15 UTC  
> Closed at: 2017-11-15 23:52:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/281  

Maybe will fix and MSVC 8 too. The fix found accidentally.  
  
<details>  
  <summary>The actual error</summary>  
  
```  
compile-c-c++ ..\..\..\..\bin.v2\libs\spirit\test\support\support_regression_multi_pass_position_iterator-p3.test\msvc-9\debug\threadapi-win32\threading-multi\support_regression_multi_pass_position_iterator-p3.obj  
regression_multi_pass_position_iterator.cpp  
boost/spirit/home/qi/skip_over.hpp(27) : error C3767: '!=': candidate function(s) not accessible  
        could be the friend function at 'boost/spirit/home/qi/skip_flag.hpp(24)' : '!='  [may be found via argument-dependent lookup]  
        or the friend function at       'boost/spirit/home/qi/skip_flag.hpp(24)' : '!='  [may be found via argument-dependent lookup]  
        or the friend function at       'boost/spirit/home/qi/skip_flag.hpp(24)' : '!='  [may be found via argument-dependent lookup]  
        boost/spirit/home/qi/directive/lexeme.hpp(64) : see reference to function template instantiation 'void boost::spirit::qi::skip_over<Iterator,Skipper>(Iterator &,const Iterator &,const T &)' being compiled  
        with  
        [  
            Iterator=iterator_type,  
            Skipper=boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::blank,boost::spirit::char_encoding::standard>>,  
            T=boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::blank,boost::spirit::char_encoding::standard>>  
        ]  
        boost/spirit/home/qi/nonterminal/detail/parser_binder.hpp(43) : see reference to function template instantiation 'bool boost::spirit::qi::lexeme_directive<Subject>::parse<Iterator,Context,Skipper,T>(Iterator &,const Iterator &,Context &,const Skipper &,Attribute &) const' being compiled  
        with  
        [  
            Subject=boost::spirit::qi::plus<boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::alpha,boost::spirit::char_encoding::ascii>>>,  
            Iterator=iterator_type,  
            Context=boost::spirit::context<boost::fusion::cons<std::basic_string<char,std::char_traits<char>,std::allocator<char>> &,boost::fusion::nil_>,boost::fusion::vector0<>>,  
            Skipper=boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::blank,boost::spirit::char_encoding::standard>>,  
            T=std::basic_string<char,std::char_traits<char>,std::allocator<char>>,  
            Attribute=std::basic_string<char,std::char_traits<char>,std::allocator<char>>  
        ]  
        boost/spirit/home/qi/nonterminal/detail/parser_binder.hpp(53) : see reference to function template instantiation 'bool boost::spirit::qi::detail::parser_binder<Parser,Auto>::call<Iterator,Skipper,Context>(Iterator &,const Iterator &,Context &,const Skipper &,boost::mpl::false_) const' being compiled  
        with  
        [  
            Parser=boost::spirit::qi::lexeme_directive<boost::spirit::qi::plus<boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::alpha,boost::spirit::char_encoding::ascii>>>>,  
            Auto=boost::mpl::false_,  
            Iterator=iterator_type,  
            Skipper=boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::blank,boost::spirit::char_encoding::standard>>,  
            Context=boost::spirit::context<boost::fusion::cons<std::basic_string<char,std::char_traits<char>,std::allocator<char>> &,boost::fusion::nil_>,boost::fusion::vector0<>>  
        ]  
        boost/function/function_template.hpp(138) : see reference to function template instantiation 'bool boost::spirit::qi::detail::parser_binder<Parser,Auto>::operator ()<Iterator,boost::spirit::qi::char_class<Tag>,boost::spirit::context<Attributes,Locals>>(Iterator &,const Iterator &,Context &,const Skipper &) const' being compiled  
        with  
        [  
            Parser=boost::spirit::qi::lexeme_directive<boost::spirit::qi::plus<boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::alpha,boost::spirit::char_encoding::ascii>>>>,  
            Auto=boost::mpl::false_,  
            Iterator=iterator_type,  
            Tag=boost::spirit::tag::char_code<boost::spirit::tag::blank,boost::spirit::char_encoding::standard>,  
            Attributes=boost::fusion::cons<std::basic_string<char,std::char_traits<char>,std::allocator<char>> &,boost::fusion::nil_>,  
            Locals=boost::fusion::vector0<>,  
            Context=boost::spirit::context<boost::fusion::cons<std::basic_string<char,std::char_traits<char>,std::allocator<char>> &,boost::fusion::nil_>,boost::fusion::vector0<>>,  
            Skipper=boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::blank,boost::spirit::char_encoding::standard>>  
        ]  
        boost/function/function_template.hpp(132) : while compiling class template member function 'bool boost::detail::function::function_obj_invoker4<FunctionObj,R,T0,T1,T2,T3>::invoke(boost::detail::function::function_buffer &,T0,T1,T2,T3)'  
        with  
        [  
            FunctionObj=boost::spirit::qi::detail::parser_binder<boost::spirit::qi::lexeme_directive<boost::spirit::qi::plus<boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::alpha,boost::spirit::char_encoding::ascii>>>>,boost::mpl::false_>,  
            R=bool,  
            T0=boost::spirit::classic::position_iterator<std::_Vector_const_iterator<char,std::allocator<char>>> &,  
            T1=const boost::spirit::classic::position_iterator<std::_Vector_const_iterator<char,std::allocator<char>>> &,  
            T2=boost::spirit::context<boost::fusion::cons<std::basic_string<char,std::char_traits<char>,std::allocator<char>> &,boost::fusion::nil_>,boost::fusion::vector0<>> &,  
            T3=const boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::blank,boost::spirit::char_encoding::standard>> &  
        ]  
        boost/function/function_template.hpp(925) : see reference to class template instantiation 'boost::detail::function::function_obj_invoker4<FunctionObj,R,T0,T1,T2,T3>' being compiled  
        with  
        [  
            FunctionObj=boost::spirit::qi::detail::parser_binder<boost::spirit::qi::lexeme_directive<boost::spirit::qi::plus<boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::alpha,boost::spirit::char_encoding::ascii>>>>,boost::mpl::false_>,  
            R=bool,  
            T0=boost::spirit::classic::position_iterator<std::_Vector_const_iterator<char,std::allocator<char>>> &,  
            T1=const boost::spirit::classic::position_iterator<std::_Vector_const_iterator<char,std::allocator<char>>> &,  
            T2=boost::spirit::context<boost::fusion::cons<std::basic_string<char,std::char_traits<char>,std::allocator<char>> &,boost::fusion::nil_>,boost::fusion::vector0<>> &,  
            T3=const boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::blank,boost::spirit::char_encoding::standard>> &  
        ]  
        boost/function/function_template.hpp(716) : see reference to function template instantiation 'void boost::function4<R,T0,T1,T2,T3>::assign_to<Functor>(Functor)' being compiled  
        with  
        [  
            R=bool,  
            T0=boost::spirit::classic::position_iterator<std::_Vector_const_iterator<char,std::allocator<char>>> &,  
            T1=const boost::spirit::classic::position_iterator<std::_Vector_const_iterator<char,std::allocator<char>>> &,  
            T2=boost::spirit::context<boost::fusion::cons<std::basic_string<char,std::char_traits<char>,std::allocator<char>> &,boost::fusion::nil_>,boost::fusion::vector0<>> &,  
            T3=const boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::blank,boost::spirit::char_encoding::standard>> &,  
            Functor=boost::spirit::qi::detail::parser_binder<boost::spirit::qi::lexeme_directive<boost::spirit::qi::plus<boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::alpha,boost::spirit::char_encoding::ascii>>>>,boost::mpl::false_>  
        ]  
        boost/function/function_template.hpp(1062) : see reference to function template instantiation 'boost::function4<R,T0,T1,T2,T3>::function4<boost::spirit::qi::detail::parser_binder<Parser,Auto>>(Functor,int)' being compiled  
        with  
        [  
            R=bool,  
            T0=boost::spirit::classic::position_iterator<std::_Vector_const_iterator<char,std::allocator<char>>> &,  
            T1=const boost::spirit::classic::position_iterator<std::_Vector_const_iterator<char,std::allocator<char>>> &,  
            T2=boost::spirit::context<boost::fusion::cons<std::basic_string<char,std::char_traits<char>,std::allocator<char>> &,boost::fusion::nil_>,boost::fusion::vector0<>> &,  
            T3=const boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::blank,boost::spirit::char_encoding::standard>> &,  
            Parser=boost::spirit::qi::lexeme_directive<boost::spirit::qi::plus<boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::alpha,boost::spirit::char_encoding::ascii>>>>,  
            Auto=boost::mpl::false_,  
            Functor=boost::spirit::qi::detail::parser_binder<boost::spirit::qi::lexeme_directive<boost::spirit::qi::plus<boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::alpha,boost::spirit::char_encoding::ascii>>>>,boost::mpl::false_>  
        ]  
        boost/function/function_template.hpp(1114) : see reference to function template instantiation 'boost::function<Signature>::function<boost::spirit::qi::detail::parser_binder<Parser,Auto>>(Functor,int)' being compiled  
        with  
        [  
            Signature=bool (iterator_type &,const iterator_type &,boost::spirit::context<boost::fusion::cons<std::basic_string<char,std::char_traits<char>,std::allocator<char>> &,boost::fusion::nil_>,boost::fusion::vector0<>> &,const boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::blank,boost::spirit::char_encoding::standard>> &),  
            Parser=boost::spirit::qi::lexeme_directive<boost::spirit::qi::plus<boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::alpha,boost::spirit::char_encoding::ascii>>>>,  
            Auto=boost::mpl::false_,  
            Functor=boost::spirit::qi::detail::parser_binder<boost::spirit::qi::lexeme_directive<boost::spirit::qi::plus<boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::alpha,boost::spirit::char_encoding::ascii>>>>,boost::mpl::false_>  
        ]  
        boost/spirit/home/qi/nonterminal/rule.hpp(184) : see reference to function template instantiation 'boost::function<Signature> &boost::function<Signature>::operator =<boost::spirit::qi::detail::parser_binder<Parser,Auto>>(Functor)' being compiled  
        with  
        [  
            Signature=bool (iterator_type &,const iterator_type &,boost::spirit::context<boost::fusion::cons<std::basic_string<char,std::char_traits<char>,std::allocator<char>> &,boost::fusion::nil_>,boost::fusion::vector0<>> &,const boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::blank,boost::spirit::char_encoding::standard>> &),  
            Parser=boost::spirit::qi::lexeme_directive<boost::spirit::qi::plus<boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::alpha,boost::spirit::char_encoding::ascii>>>>,  
            Auto=boost::mpl::false_,  
            Functor=boost::spirit::qi::detail::parser_binder<boost::spirit::qi::lexeme_directive<boost::spirit::qi::plus<boost::spirit::qi::char_class<boost::spirit::tag::char_code<boost::spirit::tag::alpha,boost::spirit::char_encoding::ascii>>>>,boost::mpl::false_>  
        ]  
        boost/spirit/home/qi/nonterminal/rule.hpp(192) : see reference to function template instantiation 'void boost::spirit::qi::rule<Iterator,T1,T2>::define<boost::mpl::false_,Expr>(boost::spirit::qi::rule<Iterator,T1,T2> &,const Expr &,boost::mpl::true_)' being compiled  
        with  
        [  
            Iterator=iterator_type,  
            T1=std::string (void),  
            T2=boost::spirit::standard::blank_type,  
            Expr=boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<boost::spirit::tag::lexeme>,0> &,const boost::proto::exprns_::expr<boost::proto::tagns_::tag::unary_plus,boost::proto::argsns_::list1<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<boost::spirit::tag::char_code<boost::spirit::tag::alpha,boost::spirit::char_encoding::ascii>>,0> &>,1> &>,2>  
        ]  
        regression_multi_pass_position_iterator.cpp(22) : see reference to function template instantiation 'boost::spirit::qi::rule<Iterator,T1,T2>::rule<boost::proto::exprns_::expr<Tag,Args,Arity>>(const Expr &,const std::string &)' being compiled  
        with  
        [  
            Iterator=iterator_type,  
            T1=std::string (void),  
            T2=boost::spirit::standard::blank_type,  
            Tag=boost::proto::tagns_::tag::subscript,  
            Args=boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<boost::spirit::tag::lexeme>,0> &,const boost::proto::exprns_::expr<boost::proto::tagns_::tag::unary_plus,boost::proto::argsns_::list1<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<boost::spirit::tag::char_code<boost::spirit::tag::alpha,boost::spirit::char_encoding::ascii>>,0> &>,1> &>,  
            Arity=2,  
            Expr=boost::proto::exprns_::expr<boost::proto::tagns_::tag::subscript,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<boost::spirit::tag::lexeme>,0> &,const boost::proto::exprns_::expr<boost::proto::tagns_::tag::unary_plus,boost::proto::argsns_::list1<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<boost::spirit::tag::char_code<boost::spirit::tag::alpha,boost::spirit::char_encoding::ascii>>,0> &>,1> &>,2>  
        ]  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const _Ty &,const std::complex<T> &)' : could not deduce template argument for 'const std::complex<T> &' from 'const iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xcomplex(168) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const _Ty &,const std::complex<T> &)' : could not deduce template argument for 'const std::complex<T> &' from 'const iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xcomplex(168) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const _Ty &,const std::complex<T> &)' : could not deduce template argument for 'const std::complex<T> &' from 'const iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xcomplex(168) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const _Ty &,const std::complex<T> &)' : could not deduce template argument for 'const std::complex<T> &' from 'const iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xcomplex(168) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const _Ty &,const std::complex<T> &)' : could not deduce template argument for 'const std::complex<T> &' from 'const iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xcomplex(168) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::complex<T> &,const _Ty &)' : could not deduce template argument for 'const std::complex<T> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xcomplex(161) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::complex<T> &,const _Ty &)' : could not deduce template argument for 'const std::complex<T> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xcomplex(161) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::complex<T> &,const _Ty &)' : could not deduce template argument for 'const std::complex<T> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xcomplex(161) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::complex<T> &,const _Ty &)' : could not deduce template argument for 'const std::complex<T> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xcomplex(161) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::complex<T> &,const _Ty &)' : could not deduce template argument for 'const std::complex<T> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xcomplex(161) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::complex<T> &,const std::complex<T> &)' : could not deduce template argument for 'const std::complex<T> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xcomplex(154) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::complex<T> &,const std::complex<T> &)' : could not deduce template argument for 'const std::complex<T> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xcomplex(154) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::complex<T> &,const std::complex<T> &)' : could not deduce template argument for 'const std::complex<T> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xcomplex(154) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::complex<T> &,const std::complex<T> &)' : could not deduce template argument for 'const std::complex<T> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xcomplex(154) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::complex<T> &,const std::complex<T> &)' : could not deduce template argument for 'const std::complex<T> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xcomplex(154) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::list<T,A> &,const std::list<T,A> &)' : could not deduce template argument for 'const std::list<T,A> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\list(1302) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::list<T,A> &,const std::list<T,A> &)' : could not deduce template argument for 'const std::list<T,A> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\list(1302) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::list<T,A> &,const std::list<T,A> &)' : could not deduce template argument for 'const std::list<T,A> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\list(1302) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::list<T,A> &,const std::list<T,A> &)' : could not deduce template argument for 'const std::list<T,A> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\list(1302) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::list<T,A> &,const std::list<T,A> &)' : could not deduce template argument for 'const std::list<T,A> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\list(1302) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::basic_string<_Elem,_Traits,_Alloc> &,const _Elem *)' : could not deduce template argument for 'const std::basic_string<_Elem,_Traits,_Alloc> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\string(120) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::basic_string<_Elem,_Traits,_Alloc> &,const _Elem *)' : could not deduce template argument for 'const std::basic_string<_Elem,_Traits,_Alloc> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\string(120) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::basic_string<_Elem,_Traits,_Alloc> &,const _Elem *)' : could not deduce template argument for 'const std::basic_string<_Elem,_Traits,_Alloc> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\string(120) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::basic_string<_Elem,_Traits,_Alloc> &,const _Elem *)' : could not deduce template argument for 'const std::basic_string<_Elem,_Traits,_Alloc> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\string(120) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::basic_string<_Elem,_Traits,_Alloc> &,const _Elem *)' : could not deduce template argument for 'const std::basic_string<_Elem,_Traits,_Alloc> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\string(120) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const _Elem *,const std::basic_string<_Elem,_Traits,_Alloc> &)' : could not deduce template argument for 'const _Elem *' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\string(110) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::basic_string<_Elem,_Traits,_Alloc> &,const std::basic_string<_Elem,_Traits,_Alloc> &)' : could not deduce template argument for 'const std::basic_string<_Elem,_Traits,_Alloc> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\string(100) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::basic_string<_Elem,_Traits,_Alloc> &,const std::basic_string<_Elem,_Traits,_Alloc> &)' : could not deduce template argument for 'const std::basic_string<_Elem,_Traits,_Alloc> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\string(100) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::basic_string<_Elem,_Traits,_Alloc> &,const std::basic_string<_Elem,_Traits,_Alloc> &)' : could not deduce template argument for 'const std::basic_string<_Elem,_Traits,_Alloc> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\string(100) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::basic_string<_Elem,_Traits,_Alloc> &,const std::basic_string<_Elem,_Traits,_Alloc> &)' : could not deduce template argument for 'const std::basic_string<_Elem,_Traits,_Alloc> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\string(100) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::basic_string<_Elem,_Traits,_Alloc> &,const std::basic_string<_Elem,_Traits,_Alloc> &)' : could not deduce template argument for 'const std::basic_string<_Elem,_Traits,_Alloc> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\string(100) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::istreambuf_iterator<_Elem,_Traits> &,const std::istreambuf_iterator<_Elem,_Traits> &)' : could not deduce template argument for 'const std::istreambuf_iterator<_Elem,_Traits> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\streambuf(557) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::istreambuf_iterator<_Elem,_Traits> &,const std::istreambuf_iterator<_Elem,_Traits> &)' : could not deduce template argument for 'const std::istreambuf_iterator<_Elem,_Traits> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\streambuf(557) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::istreambuf_iterator<_Elem,_Traits> &,const std::istreambuf_iterator<_Elem,_Traits> &)' : could not deduce template argument for 'const std::istreambuf_iterator<_Elem,_Traits> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\streambuf(557) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::istreambuf_iterator<_Elem,_Traits> &,const std::istreambuf_iterator<_Elem,_Traits> &)' : could not deduce template argument for 'const std::istreambuf_iterator<_Elem,_Traits> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\streambuf(557) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::istreambuf_iterator<_Elem,_Traits> &,const std::istreambuf_iterator<_Elem,_Traits> &)' : could not deduce template argument for 'const std::istreambuf_iterator<_Elem,_Traits> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\streambuf(557) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::vector<_Ty,_Alloc> &,const std::vector<_Ty,_Alloc> &)' : could not deduce template argument for 'const std::vector<_Ty,_Alloc> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\vector(1314) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::vector<_Ty,_Alloc> &,const std::vector<_Ty,_Alloc> &)' : could not deduce template argument for 'const std::vector<_Ty,_Alloc> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\vector(1314) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::vector<_Ty,_Alloc> &,const std::vector<_Ty,_Alloc> &)' : could not deduce template argument for 'const std::vector<_Ty,_Alloc> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\vector(1314) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::vector<_Ty,_Alloc> &,const std::vector<_Ty,_Alloc> &)' : could not deduce template argument for 'const std::vector<_Ty,_Alloc> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\vector(1314) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::vector<_Ty,_Alloc> &,const std::vector<_Ty,_Alloc> &)' : could not deduce template argument for 'const std::vector<_Ty,_Alloc> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\vector(1314) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::allocator<_Ty> &,const std::allocator<_Other> &) throw()' : could not deduce template argument for 'const std::allocator<_Ty> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xmemory(180) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::allocator<_Ty> &,const std::allocator<_Other> &) throw()' : could not deduce template argument for 'const std::allocator<_Ty> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xmemory(180) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::allocator<_Ty> &,const std::allocator<_Other> &) throw()' : could not deduce template argument for 'const std::allocator<_Ty> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xmemory(180) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::allocator<_Ty> &,const std::allocator<_Other> &) throw()' : could not deduce template argument for 'const std::allocator<_Ty> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xmemory(180) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::allocator<_Ty> &,const std::allocator<_Other> &) throw()' : could not deduce template argument for 'const std::allocator<_Ty> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xmemory(180) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::istream_iterator<_Ty,_Elem,_Traits,_Diff> &,const std::istream_iterator<_Ty,_Elem,_Traits,_Diff> &)' : could not deduce template argument for 'const std::istream_iterator<_Ty,_Elem,_Traits,_Diff> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\iterator(277) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::istream_iterator<_Ty,_Elem,_Traits,_Diff> &,const std::istream_iterator<_Ty,_Elem,_Traits,_Diff> &)' : could not deduce template argument for 'const std::istream_iterator<_Ty,_Elem,_Traits,_Diff> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\iterator(277) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::istream_iterator<_Ty,_Elem,_Traits,_Diff> &,const std::istream_iterator<_Ty,_Elem,_Traits,_Diff> &)' : could not deduce template argument for 'const std::istream_iterator<_Ty,_Elem,_Traits,_Diff> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\iterator(277) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::istream_iterator<_Ty,_Elem,_Traits,_Diff> &,const std::istream_iterator<_Ty,_Elem,_Traits,_Diff> &)' : could not deduce template argument for 'const std::istream_iterator<_Ty,_Elem,_Traits,_Diff> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\iterator(277) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::istream_iterator<_Ty,_Elem,_Traits,_Diff> &,const std::istream_iterator<_Ty,_Elem,_Traits,_Diff> &)' : could not deduce template argument for 'const std::istream_iterator<_Ty,_Elem,_Traits,_Diff> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\iterator(277) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::reverse_iterator<_RanIt> &,const std::reverse_iterator<_RanIt2> &)' : could not deduce template argument for 'const std::reverse_iterator<_RanIt> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xutility(2254) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::reverse_iterator<_RanIt> &,const std::reverse_iterator<_RanIt2> &)' : could not deduce template argument for 'const std::reverse_iterator<_RanIt> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xutility(2254) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::reverse_iterator<_RanIt> &,const std::reverse_iterator<_RanIt2> &)' : could not deduce template argument for 'const std::reverse_iterator<_RanIt> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xutility(2254) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::reverse_iterator<_RanIt> &,const std::reverse_iterator<_RanIt2> &)' : could not deduce template argument for 'const std::reverse_iterator<_RanIt> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xutility(2254) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::reverse_iterator<_RanIt> &,const std::reverse_iterator<_RanIt2> &)' : could not deduce template argument for 'const std::reverse_iterator<_RanIt> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xutility(2254) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::_Revranit<_RanIt,_Base> &,const std::_Revranit<_RanIt2,_Base2> &)' : could not deduce template argument for 'const std::_Revranit<_RanIt,_Base> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xutility(2061) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::_Revranit<_RanIt,_Base> &,const std::_Revranit<_RanIt2,_Base2> &)' : could not deduce template argument for 'const std::_Revranit<_RanIt,_Base> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xutility(2061) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::_Revranit<_RanIt,_Base> &,const std::_Revranit<_RanIt2,_Base2> &)' : could not deduce template argument for 'const std::_Revranit<_RanIt,_Base> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xutility(2061) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::_Revranit<_RanIt,_Base> &,const std::_Revranit<_RanIt2,_Base2> &)' : could not deduce template argument for 'const std::_Revranit<_RanIt,_Base> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xutility(2061) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::_Revranit<_RanIt,_Base> &,const std::_Revranit<_RanIt2,_Base2> &)' : could not deduce template argument for 'const std::_Revranit<_RanIt,_Base> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\xutility(2061) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::pair<_Ty1,_Ty2> &,const std::pair<_Ty1,_Ty2> &)' : could not deduce template argument for 'const std::pair<_Ty1,_Ty2> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\utility(91) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::pair<_Ty1,_Ty2> &,const std::pair<_Ty1,_Ty2> &)' : could not deduce template argument for 'const std::pair<_Ty1,_Ty2> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\utility(91) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::pair<_Ty1,_Ty2> &,const std::pair<_Ty1,_Ty2> &)' : could not deduce template argument for 'const std::pair<_Ty1,_Ty2> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\utility(91) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::pair<_Ty1,_Ty2> &,const std::pair<_Ty1,_Ty2> &)' : could not deduce template argument for 'const std::pair<_Ty1,_Ty2> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\utility(91) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2784: 'bool std::operator !=(const std::pair<_Ty1,_Ty2> &,const std::pair<_Ty1,_Ty2> &)' : could not deduce template argument for 'const std::pair<_Ty1,_Ty2> &' from 'iterator_type'  
        C:\Program Files (x86)\Microsoft Visual Studio 9.0\VC\INCLUDE\utility(91) : see declaration of 'std::operator !='  
boost/spirit/home/qi/skip_over.hpp(27) : error C2676: binary '!=' : 'iterator_type' does not define this operator or a conversion to a type acceptable to the predefined operator  
boost/spirit/home/qi/skip_over.hpp(27) : fatal error C1903: unable to recover from previous error(s); stopping compilation  
  
    call "%TEMP%\b2_msvc_9.0_vcvars32_.cmd" >nul  
cl /Zm800 -nologo @"..\..\..\..\bin.v2\libs\spirit\test\support\support_regression_multi_pass_position_iterator-p3.test\msvc-9\debug\threadapi-win32\threading-multi\support_regression_multi_pass_position_iterator-p3.obj.rsp"   
  
...failed compile-c-c++ ..\..\..\..\bin.v2\libs\spirit\test\support\support_regression_multi_pass_position_iterator-p3.test\msvc-9\debug\threadapi-win32\threading-multi\support_regression_multi_pass_position_iterator-p3.obj...  
  
```  
  
</details>

---
