# #587 - GCC suggest-override warnings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-13 00:35:16 UTC  
> Updated at: 2020-05-24 12:31:30 UTC  
> Closed at: 2020-05-24 12:31:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/587  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`. Duplicated warnings from same location are omitted:  
  
<pre>  
./boost/spirit/home/classic/core/non_terminal/impl/grammar.ipp:173:9: warning: ‘int boost::spirit::classic::impl::grammar_helper<GrammarT, DerivedT, ScannerT>::undefine(boost::spirit::classic::impl::grammar_helper<GrammarT, DerivedT, ScannerT>::grammar_t*) [with GrammarT = boost::spirit::classic::grammar<boost::wave::grammars::chlit_grammar, boost::spirit::classic::closure_context<boost::wave::grammars::closures::chlit_closure> >; DerivedT = boost::wave::grammars::chlit_grammar; ScannerT = boost::spirit::classic::scanner<>; boost::spirit::classic::impl::grammar_helper<GrammarT, DerivedT, ScannerT>::grammar_t = boost::spirit::classic::grammar<boost::wave::grammars::chlit_grammar, boost::spirit::classic::closure_context<boost::wave::grammars::closures::chlit_closure> >]’ can be marked override [-Wsuggest-override]  
./boost/spirit/home/classic/core/non_terminal/impl/rule.ipp:239:13: warning: ‘typename boost::spirit::classic::match_result<ScannerT, AttrT>::type boost::spirit::classic::impl::concrete_parser<ParserT, ScannerT, AttrT>::do_parse_virtual(const ScannerT&) const [with ParserT = boost::spirit::classic::action<boost::spirit::classic::rule<boost::spirit::classic::scanner<__gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char> >, boost::spirit::classic::scanner_policies<> >, boost::spirit::classic::nil_t, boost::spirit::classic::nil_t>, boost::archive::xml::append_string<std::__cxx11::basic_string<char>, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > >; ScannerT = boost::spirit::classic::scanner<__gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char> >, boost::spirit::classic::scanner_policies<> >; AttrT = boost::spirit::classic::nil_t; typename boost::spirit::classic::match_result<ScannerT, AttrT>::type = boost::spirit::classic::match<boost::spirit::classic::nil_t>]’ can be marked override [-Wsuggest-override]  
./boost/spirit/home/classic/core/non_terminal/impl/rule.ipp:245:13: warning: ‘boost::spirit::classic::impl::abstract_parser<ScannerT, AttrT>* boost::spirit::classic::impl::concrete_parser<ParserT, ScannerT, AttrT>::clone() const [with ParserT = boost::spirit::classic::action<boost::spirit::classic::rule<boost::spirit::classic::scanner<__gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char> >, boost::spirit::classic::scanner_policies<> >, boost::spirit::classic::nil_t, boost::spirit::classic::nil_t>, boost::archive::xml::append_string<std::__cxx11::basic_string<char>, __gnu_cxx::__normal_iterator<const char*, std::__cxx11::basic_string<char> > > >; ScannerT = boost::spirit::classic::scanner<__gnu_cxx::__normal_iterator<char*, std::__cxx11::basic_string<char> >, boost::spirit::classic::scanner_policies<> >; AttrT = boost::spirit::classic::nil_t]’ can be marked override [-Wsuggest-override]  
./boost/spirit/home/classic/iterator/multi_pass.hpp:156:5: warning: ‘virtual const char* boost::spirit::classic::multi_pass_policies::illegal_backtracking::what() const’ can be marked override [-Wsuggest-override]  
./boost/spirit/home/support/detail/hold_any.hpp:52:29: warning: ‘virtual const char* boost::spirit::bad_any_cast::what() const’ can be marked override [-Wsuggest-override]  
./boost/spirit/home/support/iterators/detail/buf_id_check_policy.hpp:29:21: warning: ‘virtual const char* boost::spirit::iterator_policies::illegal_backtracking::what() const’ can be marked override [-Wsuggest-override]  
</pre>  
  
`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 1

> Username: djowel  
> Created at: 2020-04-13 22:18:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/587#issuecomment-613124937  

I'd welcome a PR if yo uare up to it.

---

## Comment 2

> Username: EugeneZelenko  
> Created at: 2020-05-23 20:46:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/587#issuecomment-633137000  

See fixes in #600.
