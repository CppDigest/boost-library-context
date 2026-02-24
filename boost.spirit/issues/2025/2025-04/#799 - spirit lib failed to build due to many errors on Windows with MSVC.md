# #799 - spirit lib failed to build due to many errors on Windows with MSVC [Closed]

> Username: LinWrSpace  
> Created at: 2025-04-02 09:50:10 UTC  
> Updated at: 2025-05-09 22:21:16 UTC  
> Closed at: 2025-05-09 22:19:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/799  

**Issue Description:**  
Found Boost\libs\spirit failed to build due to many errors on Windows with MSVC, this issue can be reproduced on 6f50c3c commit. Could you please take a look? Thanks in advance.  
  
**Reproduce Steps:**  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git f:\Boost  
2. open a VS 2022 x64 command prompt and browse to f:\Boost  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=C:\gitP\boostorg\boost\amd64 address-model=64 architecture=x86  
5. .\b2 variant=release --build-dir=C:\gitP\boostorg\boost\amd64 address-model=64 architecture=x86  
6. .\b2 -j16 variant=release --build-dir=C:\gitP\boostorg\boost\amd64 libs\spirit\test address-model=64  
  
[spirit_test.log](https://github.com/user-attachments/files/19565706/spirit_test.log)  
  
**ErrorMessage:**  
.\boost/spirit/home/karma/nonterminal/rule.hpp(309): error C2664: 'bool boost::function_n<R,boost::spirit::karma::detail::output_iterator<OutputIterator,boost::spirit::karma::rule<OutputIterator,boost::spirit::karma::rule<OutputIterator,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type>,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type>::properties,boost::spirit::unused_type> &,boost::spirit::context<boost::fusion::cons<const boost::spirit::unused_type &,boost::fusion::nil_>,boost::fusion::vector<>> &,const boost::spirit::karma::reference<const boost::spirit::karma::rule<OutputIterator,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type>> &>::operator ()(boost::spirit::karma::detail::output_iterator<OutputIterator,boost::spirit::karma::rule<OutputIterator,boost::spirit::karma::rule<OutputIterator,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type>,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type>::properties,boost::spirit::unused_type> &,boost::spirit::context<boost::fusion::cons<const boost::spirit::unused_type &,boost::fusion::nil_>,boost::fusion::vector<>> &,const boost::spirit::karma::reference<const boost::spirit::karma::rule<OutputIterator,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type>> &) const': cannot convert argument 3 from 'const Delimiter' to 'const boost::spirit::karma::reference<const boost::spirit::karma::rule<main::outiter_type,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type,boost::spirit::unused_type>> &'  
......  
.\boost/spirit/home/qi/nonterminal/rule.hpp(290): error C2338: static_assert failed: 'The passed skipper is not compatible/convertible to one that the rule was instantiated with'  
.\boost/spirit/home/qi/nonterminal/rule.hpp(290): note: the template instantiation context (the oldest one first) is  
libs\spirit\test\qi\grammar_fail.cpp(35): note: see reference to function template instantiation 'bool boost::spirit::qi::phrase_parse<const char*,num_list,boost::proto::exprns_::expr<Tag,Args,2>>(Iterator &,Iterator,const Expr &,const Skipper &,boost::spirit::qi::skip_flag)' being compiled  
....  
.\boost/spirit/home/x3/support/expectation.hpp(120): error C2988: unrecognizable template declaration/definition

---

## Comment 1

> Username: saki7  
> Created at: 2025-05-09 22:19:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/799#issuecomment-2867977609  

You need to enable C++17 or later when you're explicitly building `libs\spirit\test`. Please examine your log more carefully before submitting a report. If you ignore obvious messages like `The contents of <optional> are available only with C++17 or later.`, then I can't offer any help.
