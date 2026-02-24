# #420 Lex: Avoid casting `min_token_id` to `id_type` in lexer ctor [Merged]

> Username: Kojoley  
> Created at: 2018-11-20 22:31:46 UTC  
> Updated at: 2018-11-21 10:59:46 UTC  
> Merged at: 2018-11-21 10:59:41 UTC  
> Closed at: 2018-11-21 10:59:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/420  

When `min_token_id` is not a valid value for `id_type` and `first_id` is not provided lexer constructor is triggering UB. To fix this problem, constructor with ommited `first_id` initializes `next_token_id` directly, so if unique ids feature is not used there is no UB.  
  
Found the problem in [a lex test](https://github.com/boostorg/spirit/blob/develop/test/lex/regression_wide.cpp) with UBSan.  
```  
boost/spirit/home/lex/lexer/lexer.hpp:382:27: runtime error: load of value 65536, which is not a valid value for type 'boost::spirit::lex::lexer<boost::spirit::lex::lexertl::actor_lexer<boost::spirit::lex::lexertl::token<__gnu_cxx::__normal_iterator<wchar_t *, std::__cxx11::basic_string<wchar_t> >, boost::mpl::vector<wchar_t, std::__cxx11::basic_string<wchar_t>, double, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, mpl_::bool_<true>, tokenids>, __gnu_cxx::__normal_iterator<wchar_t *, std::__cxx11::basic_string<wchar_t> >, boost::spirit::lex::lexertl::functor<boost::spirit::lex::lexertl::token<__gnu_cxx::__normal_iterator<wchar_t *, std::__cxx11::basic_string<wchar_t> >, boost::mpl::vector<wchar_t, std::__cxx11::basic_string<wchar_t>, double, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::    #0 0x44b481 in boost::spirit::lex::lexer<boost::spirit::lex::lexertl::actor_lexer<boost::spirit::lex::lexertl::token<__gnu_cxx::__normal_iterator<wchar_t*, std::__cxx11::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> > >, boost::mpl::vector<wchar_t, std::__cxx11::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> >, double, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, mpl_::bool_<true>, tokenids>, __gnu_cxx::__normal_iterator<wchar_t*, std::__cxx11::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> > >, boost::spirit::lex::lexertl::functor<boost::spirit::lex::lexertl::token<__gnu_cxx::__normal_iterator<wchar_t*, std::__cxx11::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> > >, boost::mpl::vector<wchar_t, std::__cxx11::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> >, double, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, mpl_::bool_<true>, tokenids>, boost::spirit::lex::lexertl::detail::data, __gnu_cxx::__normal_iterator<wchar_t*, std::__cxx11::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> > >, mpl_::bool_<true>, mpl_::bool_<true> > > >::lexer(unsigned int, tokenids) ~/boost-root/./boost/spirit/home/lex/lexer/lexer.hpp:382:27  
    #1 0x43a5cd in mega_tokens<boost::spirit::lex::lexertl::actor_lexer<boost::spirit::lex::lexertl::token<__gnu_cxx::__normal_iterator<wchar_t*, std::__cxx11::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> > >, boost::mpl::vector<wchar_t, std::__cxx11::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> >, double, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, mpl_::bool_<true>, tokenids>, __gnu_cxx::__normal_iterator<wchar_t*, std::__cxx11::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> > >, boost::spirit::lex::lexertl::functor<boost::spirit::lex::lexertl::token<__gnu_cxx::__normal_iterator<wchar_t*, std::__cxx11::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> > >, boost::mpl::vector<wchar_t, std::__cxx11::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> >, double, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, mpl_::bool_<true>, tokenids>, boost::spirit::lex::lexertl::detail::data, __gnu_cxx::__normal_iterator<wchar_t*, std::__cxx11::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> > >, mpl_::bool_<true>, mpl_::bool_<true> > > >::mega_tokens() ~/boost-root/libs/spirit/test/lex/regression_wide.cpp:89:5  
    #2 0x437bd5 in main ~/boost-root/libs/spirit/test/lex/regression_wide.cpp:122:29  
    #3 0x7f078b0752e0 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x202e0)  
    #4 0x40cff9 in _start (~/boost-root/bin.v2/libs/spirit/test/lex/lex_regression_wide.test/undefined/clang-linux-7/debug/visibility-hidden/lex_regression_wide+0x40cff9)  
```

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2018-11-21 01:02:38 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/spirit/pull/420#pullrequestreview-177040687  

LGTM, thanks!

---
