# #399 - grammar_fail.cpp test fails [Closed]

> Username: spacelg  
> Created at: 2018-09-26 07:53:10 UTC  
> Updated at: 2018-12-17 21:47:25 UTC  
> Closed at: 2018-09-26 10:52:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/399  

Environment:  
 VS 2017 + Windows Server 2016  
  
Issue description:  
 We build and run test for Boost. And we found grammar_fail.cpp failed to build due to error C2664: 'bool boost::function4<R,T0,T1,T2,T3>::operator ()(T0,T1,T2,T3) const': cannot convert argument 4 from 'const Skipper' to 'T3'. Could you pleaes take a look? Thanks in advance.  
[log_x86_test_99.log](https://github.com/boostorg/spirit/files/2418647/log_x86_test_99.log)  
  
  
Error info:  
.\boost/spirit/home/qi/nonterminal/rule.hpp(304): error C2664: 'bool boost::function4<R,T0,T1,T2,T3>::operator ()(T0,T1,T2,T3) const': cannot convert argument 4 from 'const Skipper' to 'T3'  
        with  
        [  
            R=bool,  
            T0=const char *&,  
            T1=const char *const &,  
            T2=boost::spirit::context<boost::fusion::cons<boost::spirit::unused_type &,boost::fusion::nil_>,boost::fusion::vector<>> &,  
            T3=const boost::spirit::qi::reference<const boost::spirit::qi::rule<const char *,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type>> &  
        ]  
        and  
        [  
            Skipper=skipper_type  
        ]  
        and  
        [  
            T3=const boost::spirit::qi::reference<const boost::spirit::qi::rule<const char *,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type>> &  
        ]  
.\boost/spirit/home/qi/nonterminal/rule.hpp(304): note: Reason: cannot convert from 'const Skipper' to 'const boost::spirit::qi::reference<const boost::spirit::qi::rule<const char *,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type>>'  
        with  
        [  
            Skipper=skipper_type  
        ]  
.\boost/spirit/home/qi/nonterminal/rule.hpp(304): note: No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
.\boost/spirit/home/qi/reference.hpp(43): note: see reference to function template instantiation 'bool boost::spirit::qi::rule<Iterator,T1,T2,T3,T4>::parse<Context,Skipper,Attribute>(Iterator &,const Iterator &,Context &,const Skipper &,Attribute &) const' being compiled  
        with  
        [  
            Iterator=const char *,  
            T1=boost::spirit::qi::rule<const char *,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type>,  
            T2=boost::spirit::unused_type,  
            T3=boost::spirit::unused_type,  
            T4=boost::spirit::unused_type,  
            Context=const boost::spirit::unused_type,  
            Skipper=skipper_type,  
            Attribute=const boost::spirit::unused_type  
        ]  
.\boost/spirit/home/qi/reference.hpp(43): note: see reference to function template instantiation 'bool boost::spirit::qi::rule<Iterator,T1,T2,T3,T4>::parse<Context,Skipper,Attribute>(Iterator &,const Iterator &,Context &,const Skipper &,Attribute &) const' being compiled  
        with  
        [  
            Iterator=const char *,  
            T1=boost::spirit::qi::rule<const char *,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type>,  
            T2=boost::spirit::unused_type,  
            T3=boost::spirit::unused_type,  
            T4=boost::spirit::unused_type,  
            Context=const boost::spirit::unused_type,  
            Skipper=skipper_type,  
            Attribute=const boost::spirit::unused_type  
        ]  
.\boost/spirit/home/qi/detail/parse.hpp(85): note: see reference to function template instantiation 'bool boost::spirit::qi::reference<const boost::spirit::qi::rule<Iterator,T1,T2,T3,T4>>::parse<Iterator,const boost::spirit::unused_type,skipper_type,const boost::spirit::unused_type>(Iterator &,const Iterator &,Context &,const Skipper &,Attribute &) const' being compiled  
        with  
        [  
            Iterator=const char *,  
            T1=boost::spirit::qi::rule<const char *,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type>,  
            T2=boost::spirit::unused_type,  
            T3=boost::spirit::unused_type,  
            T4=boost::spirit::unused_type,  
            Context=const boost::spirit::unused_type,  
            Skipper=skipper_type,  
            Attribute=const boost::spirit::unused_type  
        ]  
.\boost/spirit/home/qi/detail/parse.hpp(84): note: see reference to function template instantiation 'bool boost::spirit::qi::reference<const boost::spirit::qi::rule<Iterator,T1,T2,T3,T4>>::parse<Iterator,const boost::spirit::unused_type,skipper_type,const boost::spirit::unused_type>(Iterator &,const Iterator &,Context &,const Skipper &,Attribute &) const' being compiled  
        with  
        [  
            Iterator=const char *,  
            T1=boost::spirit::qi::rule<const char *,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type>,  
            T2=boost::spirit::unused_type,  
            T3=boost::spirit::unused_type,  
            T4=boost::spirit::unused_type,  
            Context=const boost::spirit::unused_type,  
            Skipper=skipper_type,  
            Attribute=const boost::spirit::unused_type  
        ]  
.\boost/spirit/home/qi/parse.hpp(121): note: see reference to function template instantiation 'bool boost::spirit::qi::detail::phrase_parse_impl<Expr,void>::call<Iterator,Skipper>(Iterator &,Iterator,const Expr &,const Skipper &,boost::spirit::qi::skip_flag)' being compiled  
        with  
        [  
            Expr=num_list,  
            Iterator=const char *,  
            Skipper=boost::proto::exprns_::expr<boost::proto::tagns_::tag::bitwise_or,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<boost::spirit::tag::char_code<boost::spirit::tag::space,boost::spirit::char_encoding::standard>>,0> &,const boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right,boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const char &>,0>,const boost::proto::exprns_::expr<boost::proto::tagns_::tag::dereference,boost::proto::argsns_::list1<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::complement,boost::proto::argsns_::list1<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<boost::spirit::terminal_ex<boost::spirit::tag::standard::char_,boost::fusion::vector<char>>>,0> &>,1> &>,1> &>,2> &,boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const char &>,0>>,2> &>,2>  
        ]  
.\boost/spirit/home/qi/parse.hpp(120): note: see reference to function template instantiation 'bool boost::spirit::qi::detail::phrase_parse_impl<Expr,void>::call<Iterator,Skipper>(Iterator &,Iterator,const Expr &,const Skipper &,boost::spirit::qi::skip_flag)' being compiled  
        with  
        [  
            Expr=num_list,  
            Iterator=const char *,  
            Skipper=boost::proto::exprns_::expr<boost::proto::tagns_::tag::bitwise_or,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<boost::spirit::tag::char_code<boost::spirit::tag::space,boost::spirit::char_encoding::standard>>,0> &,const boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right,boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const char &>,0>,const boost::proto::exprns_::expr<boost::proto::tagns_::tag::dereference,boost::proto::argsns_::list1<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::complement,boost::proto::argsns_::list1<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<boost::spirit::terminal_ex<boost::spirit::tag::standard::char_,boost::fusion::vector<char>>>,0> &>,1> &>,1> &>,2> &,boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const char &>,0>>,2> &>,2>  
        ]  
libs\spirit\test\qi\grammar_fail.cpp(36): note: see reference to function template instantiation 'bool boost::spirit::qi::phrase_parse<const char*,num_list,boost::proto::exprns_::expr<Tag,Args,2>>(Iterator &,Iterator,const Expr &,const Skipper &,boost::spirit::qi::skip_flag)' being compiled  
        with  
        [  
            Tag=boost::proto::tagns_::tag::bitwise_or,  
            Args=boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<boost::spirit::tag::char_code<boost::spirit::tag::space,boost::spirit::char_encoding::standard>>,0> &,const boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right,boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const char &>,0>,const boost::proto::exprns_::expr<boost::proto::tagns_::tag::dereference,boost::proto::argsns_::list1<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::complement,boost::proto::argsns_::list1<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<boost::spirit::terminal_ex<boost::spirit::tag::standard::char_,boost::fusion::vector<char>>>,0> &>,1> &>,1> &>,2> &,boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const char &>,0>>,2> &>,  
            Iterator=const char *,  
            Expr=num_list,  
            Skipper=boost::proto::exprns_::expr<boost::proto::tagns_::tag::bitwise_or,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<boost::spirit::tag::char_code<boost::spirit::tag::space,boost::spirit::char_encoding::standard>>,0> &,const boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right,boost::proto::argsns_::list2<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::shift_right,boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const char &>,0>,const boost::proto::exprns_::expr<boost::proto::tagns_::tag::dereference,boost::proto::argsns_::list1<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::complement,boost::proto::argsns_::list1<const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<boost::spirit::terminal_ex<boost::spirit::tag::standard::char_,boost::fusion::vector<char>>>,0> &>,1> &>,1> &>,2> &,boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const char &>,0>>,2> &>,2>  
        ]

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-09-26 10:52:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/399#issuecomment-424671917  

You were fooled by b2 as I were many times and even opened an issue about it https://github.com/boostorg/build/issues/262  
  
This test have to fail.  
https://github.com/boostorg/spirit/blob/925f40a2d0af5beed5c9a6d5882271d5d326e949/test/qi/grammar_fail.cpp#L28  
  
And it fails as expected:  
```  
(failed-as-expected) ..\out\x86rel\boost\bin.v2\libs\spirit\test\qi\qi_grammar_fail.test\msvc-14.1\release\threading-multi\qi_grammar_fail.obj  
```  
  
The only actual failing test you have is `x3_binary`, it will be fixed after merging #397.
