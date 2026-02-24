# #483 lexertl: Missing member initialization [Merged]

> Username: Kojoley  
> Created at: 2019-03-15 23:55:19 UTC  
> Updated at: 2019-03-28 13:54:05 UTC  
> Merged at: 2019-03-28 13:54:02 UTC  
> Closed at: 2019-03-28 13:54:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/483  

Fixes a GCC -Wuninitialized warning in lex/regression_syntax_error which is  
most likely a false positive https://gcc.gnu.org/bugzilla/show_bug.cgi?id=89733  
  
```cpp  
gcc.compile.c++ ../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-2a-iso/threading-multi/visibility-hidden/regression_syntax_error.o  
In file included from ../../../../boost/spirit/home/lex/lexer/lexertl/lexer.hpp:22,  
                 from ../../../../boost/spirit/home/lex/lexer_lexertl.hpp:16,  
                 from ../../../../boost/spirit/include/lex_lexertl.hpp:16,  
                 from regression_syntax_error.cpp:10:  
../../../../boost/spirit/home/lex/lexer/lexertl/functor_data.hpp: In function ‘bool boost::spirit::lex::tokenize_and_parse(Iterator&, Iterator, const Lexer&, const ParserExpr&) [with Iterator = const char*; Lexer = my_lexer<boost::spirit::lex::lexertl::actor_lexer<boost::spirit::lex::lexertl::token<const char*, boost::mpl::vector<std::__cxx11::basic_string<char>, double, int> > > >; ParserExpr = my_grammar<boost::spirit::lex::lexertl::iterator<boost::spirit::lex::lexertl::functor<boost::spirit::lex::lexertl::token<const char*, boost::mpl::vector<std::__cxx11::basic_string<char>, double, int> >, boost::spirit::lex::lexertl::detail::data, const char*, mpl_::bool_<true>, mpl_::bool_<true> > > >]’:  
../../../../boost/spirit/home/lex/lexer/lexertl/functor_data.hpp:277:15: error: ‘<anonymous>.boost::spirit::lex::lexertl::detail::data<const char*, mpl_::bool_<true>, mpl_::bool_<true>, boost::variant<boost::detail::variant::over_sequence<boost::mpl::l_item<mpl_::long_<4>, boost::iterator_range<const char*>, boost::mpl::l_item<mpl_::long_<3>, std::__cxx11::basic_string<char>, boost::mpl::l_item<mpl_::long_<2>, double, boost::mpl::l_item<mpl_::long_<1>, int, boost::mpl::l_end> > > > > > >::end_’ is used uninitialized in this function [-Werror=uninitialized]  
         class data<Iterator, mpl::true_, HasState, TokenValue>  
               ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cc1plus: all warnings being treated as errors  
    "ccache" "g++-8"  -ftemplate-depth-512 -std=c++2a -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -Werror -fvisibility=hidden -Wno-maybe-uninitialized -Wno-sign-compare  -DBOOST_ALL_NO_LIB=1 -DNDEBUG  -I"." -I"../../../.." -c -o "../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-2a-iso/threading-multi/visibility-hidden/regression_syntax_error.o" "regression_syntax_error.cpp"  
...failed gcc.compile.c++ ../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-2a-iso/threading-multi/visibility-hidden/regression_syntax_error.o...  
...skipped <p../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-2a-iso/threading-multi/visibility-hidden>lex_regression_syntax_error for lack of <p../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-2a-iso/threading-multi/visibility-hidden>regression_syntax_error.o...  
...skipped <p../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-2a-iso/threading-multi/visibility-hidden>lex_regression_syntax_error.run for lack of <p../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-2a-iso/threading-multi/visibility-hidden>lex_regression_syntax_error...  
gcc.compile.c++ ../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-17-iso/threading-multi/visibility-hidden/regression_syntax_error.o  
In file included from ../../../../boost/spirit/home/lex/lexer/lexertl/lexer.hpp:22,  
                 from ../../../../boost/spirit/home/lex/lexer_lexertl.hpp:16,  
                 from ../../../../boost/spirit/include/lex_lexertl.hpp:16,  
                 from regression_syntax_error.cpp:10:  
../../../../boost/spirit/home/lex/lexer/lexertl/functor_data.hpp: In function ‘bool boost::spirit::lex::tokenize_and_parse(Iterator&, Iterator, const Lexer&, const ParserExpr&) [with Iterator = const char*; Lexer = my_lexer<boost::spirit::lex::lexertl::actor_lexer<boost::spirit::lex::lexertl::token<const char*, boost::mpl::vector<std::__cxx11::basic_string<char>, double, int> > > >; ParserExpr = my_grammar<boost::spirit::lex::lexertl::iterator<boost::spirit::lex::lexertl::functor<boost::spirit::lex::lexertl::token<const char*, boost::mpl::vector<std::__cxx11::basic_string<char>, double, int> >, boost::spirit::lex::lexertl::detail::data, const char*, mpl_::bool_<true>, mpl_::bool_<true> > > >]’:  
../../../../boost/spirit/home/lex/lexer/lexertl/functor_data.hpp:277:15: error: ‘<anonymous>.boost::spirit::lex::lexertl::detail::data<const char*, mpl_::bool_<true>, mpl_::bool_<true>, boost::variant<boost::detail::variant::over_sequence<boost::mpl::l_item<mpl_::long_<4>, boost::iterator_range<const char*>, boost::mpl::l_item<mpl_::long_<3>, std::__cxx11::basic_string<char>, boost::mpl::l_item<mpl_::long_<2>, double, boost::mpl::l_item<mpl_::long_<1>, int, boost::mpl::l_end> > > > > > >::end_’ is used uninitialized in this function [-Werror=uninitialized]  
         class data<Iterator, mpl::true_, HasState, TokenValue>  
               ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cc1plus: all warnings being treated as errors  
    "ccache" "g++-8"  -ftemplate-depth-512 -std=c++17 -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -Werror -fvisibility=hidden -Wno-maybe-uninitialized -Wno-sign-compare  -DBOOST_ALL_NO_LIB=1 -DNDEBUG  -I"." -I"../../../.." -c -o "../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-17-iso/threading-multi/visibility-hidden/regression_syntax_error.o" "regression_syntax_error.cpp"  
...failed gcc.compile.c++ ../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-17-iso/threading-multi/visibility-hidden/regression_syntax_error.o...  
...skipped <p../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-17-iso/threading-multi/visibility-hidden>lex_regression_syntax_error for lack of <p../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-17-iso/threading-multi/visibility-hidden>regression_syntax_error.o...  
...skipped <p../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-17-iso/threading-multi/visibility-hidden>lex_regression_syntax_error.run for lack of <p../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-17-iso/threading-multi/visibility-hidden>lex_regression_syntax_error...  
gcc.compile.c++ ../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-14-iso/threading-multi/visibility-hidden/regression_syntax_error.o  
In file included from ../../../../boost/spirit/home/lex/lexer/lexertl/lexer.hpp:22,  
                 from ../../../../boost/spirit/home/lex/lexer_lexertl.hpp:16,  
                 from ../../../../boost/spirit/include/lex_lexertl.hpp:16,  
                 from regression_syntax_error.cpp:10:  
../../../../boost/spirit/home/lex/lexer/lexertl/functor_data.hpp: In function ‘bool boost::spirit::lex::tokenize_and_parse(Iterator&, Iterator, const Lexer&, const ParserExpr&) [with Iterator = const char*; Lexer = my_lexer<boost::spirit::lex::lexertl::actor_lexer<boost::spirit::lex::lexertl::token<const char*, boost::mpl::vector<std::__cxx11::basic_string<char>, double, int> > > >; ParserExpr = my_grammar<boost::spirit::lex::lexertl::iterator<boost::spirit::lex::lexertl::functor<boost::spirit::lex::lexertl::token<const char*, boost::mpl::vector<std::__cxx11::basic_string<char>, double, int> >, boost::spirit::lex::lexertl::detail::data, const char*, mpl_::bool_<true>, mpl_::bool_<true> > > >]’:  
../../../../boost/spirit/home/lex/lexer/lexertl/functor_data.hpp:277:15: error: ‘<anonymous>.boost::spirit::lex::lexertl::detail::data<const char*, mpl_::bool_<true>, mpl_::bool_<true>, boost::variant<boost::detail::variant::over_sequence<boost::mpl::l_item<mpl_::long_<4>, boost::iterator_range<const char*>, boost::mpl::l_item<mpl_::long_<3>, std::__cxx11::basic_string<char>, boost::mpl::l_item<mpl_::long_<2>, double, boost::mpl::l_item<mpl_::long_<1>, int, boost::mpl::l_end> > > > > > >::end_’ is used uninitialized in this function [-Werror=uninitialized]  
         class data<Iterator, mpl::true_, HasState, TokenValue>  
               ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cc1plus: all warnings being treated as errors  
    "ccache" "g++-8"  -ftemplate-depth-512 -std=c++14 -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -Werror -fvisibility=hidden -Wno-maybe-uninitialized -Wno-sign-compare  -DBOOST_ALL_NO_LIB=1 -DNDEBUG  -I"." -I"../../../.." -c -o "../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-14-iso/threading-multi/visibility-hidden/regression_syntax_error.o" "regression_syntax_error.cpp"  
...failed gcc.compile.c++ ../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-14-iso/threading-multi/visibility-hidden/regression_syntax_error.o...  
...skipped <p../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-14-iso/threading-multi/visibility-hidden>lex_regression_syntax_error for lack of <p../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-14-iso/threading-multi/visibility-hidden>regression_syntax_error.o...  
...skipped <p../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-14-iso/threading-multi/visibility-hidden>lex_regression_syntax_error.run for lack of <p../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-14-iso/threading-multi/visibility-hidden>lex_regression_syntax_error...  
gcc.compile.c++ ../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-11-iso/threading-multi/visibility-hidden/regression_syntax_error.o  
gcc.link ../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-11-iso/threading-multi/visibility-hidden/lex_regression_syntax_error  
testing.capture-output ../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-11-iso/threading-multi/visibility-hidden/lex_regression_syntax_error.run  
**passed** ../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-11-iso/threading-multi/visibility-hidden/lex_regression_syntax_error.test  
gcc.compile.c++ ../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-03-iso/threading-multi/visibility-hidden/regression_syntax_error.o  
gcc.link ../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-03-iso/threading-multi/visibility-hidden/lex_regression_syntax_error  
testing.capture-output ../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-03-iso/threading-multi/visibility-hidden/lex_regression_syntax_error.run  
**passed** ../../../../bin.v2/libs/spirit/test/lex/lex_regression_syntax_error.test/gcc-8/release/cxxstd-03-iso/threading-multi/visibility-hidden/lex_regression_syntax_error.test  
```

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2019-03-16 16:49:20 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/spirit/pull/483#pullrequestreview-215321435  

LGTM, thanks!

---
