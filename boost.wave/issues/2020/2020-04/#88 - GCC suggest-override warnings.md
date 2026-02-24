# #88 - GCC suggest-override warnings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-13 00:31:24 UTC  
> Updated at: 2020-04-14 04:12:35 UTC  
> Closed at: 2020-04-14 04:12:35 UTC  
> Url: https://github.com/boostorg/wave/issues/88  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`. Duplicated warnings from same location are omitted:  
  
<pre>  
./boost/wave/cpp_exceptions.hpp:164:25: warning: ‘virtual const char* boost::wave::preprocess_exception::what() const’ can be marked override [-Wsuggest-override]  
./boost/wave/cpp_exceptions.hpp:168:25: warning: ‘virtual const char* boost::wave::preprocess_exception::description() const’ can be marked override [-Wsuggest-override]  
./boost/wave/cpp_exceptions.hpp:172:17: warning: ‘virtual int boost::wave::preprocess_exception::get_severity() const’ can be marked override [-Wsuggest-override]  
./boost/wave/cpp_exceptions.hpp:176:17: warning: ‘virtual int boost::wave::preprocess_exception::get_errorcode() const’ can be marked override [-Wsuggest-override]  
./boost/wave/cpp_exceptions.hpp:180:25: warning: ‘virtual const char* boost::wave::preprocess_exception::get_related_name() const’ can be marked override [-Wsuggest-override]  
./boost/wave/cpp_exceptions.hpp:184:18: warning: ‘virtual bool boost::wave::preprocess_exception::is_recoverable() const’ can be marked override [-Wsuggest-override]  
./boost/wave/cpp_exceptions.hpp:384:25: warning: ‘virtual const char* boost::wave::macro_handling_exception::what() const’ can be marked override [-Wsuggest-override]  
./boost/wave/cpp_exceptions.hpp:388:17: warning: ‘virtual const char* boost::wave::macro_handling_exception::get_related_name() const’ can be marked override [-Wsuggest-override]  
./boost/wave/cpp_exceptions.hpp:79:25: warning: ‘virtual const char* boost::wave::cpp_exception::what() const’ can be marked override [-Wsuggest-override]  
./boost/wave/cpplexer/cpplexer_exceptions.hpp:145:25: warning: ‘virtual const char* boost::wave::cpplexer::cpplexer_exception::what() const’ can be marked override [-Wsuggest-override]  
./boost/wave/cpplexer/cpplexer_exceptions.hpp:189:25: warning: ‘virtual const char* boost::wave::cpplexer::lexing_exception::what() const’ can be marked override [-Wsuggest-override]  
./boost/wave/cpplexer/cpplexer_exceptions.hpp:193:25: warning: ‘virtual const char* boost::wave::cpplexer::lexing_exception::description() const’ can be marked override [-Wsuggest-override]  
./boost/wave/cpplexer/cpplexer_exceptions.hpp:197:17: warning: ‘virtual int boost::wave::cpplexer::lexing_exception::get_severity() const’ can be marked override [-Wsuggest-override]  
./boost/wave/cpplexer/cpplexer_exceptions.hpp:201:17: warning: ‘virtual int boost::wave::cpplexer::lexing_exception::get_errorcode() const’ can be marked override [-Wsuggest-override]  
./boost/wave/cpplexer/cpplexer_exceptions.hpp:205:18: warning: ‘virtual bool boost::wave::cpplexer::lexing_exception::is_recoverable() const’ can be marked override [-Wsuggest-override]  
./boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:346:17: warning: ‘boost::wave::cpplexer::re2clex::lex_functor<IteratorT, PositionT, TokenT>::token_type& boost::wave::cpplexer::re2clex::lex_functor<IteratorT, PositionT, TokenT>::get(boost::wave::cpplexer::re2clex::lex_functor<IteratorT, PositionT, TokenT>::token_type&) [with IteratorT = char*; PositionT = boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> > > >; TokenT = boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> > > > >; boost::wave::cpplexer::re2clex::lex_functor<IteratorT, PositionT, TokenT>::token_type = boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> > > > >]’ can be marked override [-Wsuggest-override]  
./boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:347:10: warning: ‘void boost::wave::cpplexer::re2clex::lex_functor<IteratorT, PositionT, TokenT>::set_position(const PositionT&) [with IteratorT = char*; PositionT = boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> > > >; TokenT = boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> > > > >]’ can be marked override [-Wsuggest-override]  
./boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp:349:10: warning: ‘bool boost::wave::cpplexer::re2clex::lex_functor<IteratorT, PositionT, TokenT>::has_include_guards(std::__cxx11::string&) const [with IteratorT = char*; PositionT = boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> > > >; TokenT = boost::wave::cpplexer::lex_token<boost::wave::util::file_position<boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char> > > > >; std::__cxx11::string = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
</pre>  
  
Depends on boostorg/config#329.

---

## Comment 1

> Username: jefftrull  
> Created at: 2020-04-13 05:44:31 UTC  
> Url: https://github.com/boostorg/wave/issues/88#issuecomment-612760902  

I believe Wave is staying with C++03 for now. There are a lot of things we could be doing if that were loosened :) @hkaiser any thoughts?

---

## Comment 2

> Username: hkaiser  
> Created at: 2020-04-13 13:32:38 UTC  
> Url: https://github.com/boostorg/wave/issues/88#issuecomment-612899615  

> I believe Wave is staying with C++03 for now. There are a lot of things we could be doing if that were loosened :) @hkaiser any thoughts?  
  
I'm not sure what Boost's policy is nowadays. If it was for me, I had no objections to slowly migrating the codebase to something more modern (C++17)?   
  
Alternatively - just related to the `override` keyword - I would assume Boost will  add a macro for this we could certainly use (or does is have already?).

---

## Comment 3

> Username: EugeneZelenko  
> Created at: 2020-04-13 14:07:20 UTC  
> Url: https://github.com/boostorg/wave/issues/88#issuecomment-612913213  

This is not about changing language standard. Macro, introduced in boostorg/config#329, would expand as `override` in C++11 or newer.

---

## Comment 4

> Username: jefftrull  
> Created at: 2020-04-13 14:18:39 UTC  
> Url: https://github.com/boostorg/wave/issues/88#issuecomment-612917755  

Ah, I see, that's great. I can take on making this update...  
  
Are there other macros we should be using?

---

## Comment 5

> Username: EugeneZelenko  
> Created at: 2020-04-13 14:25:11 UTC  
> Url: https://github.com/boostorg/wave/issues/88#issuecomment-612920407  

I think you should look on `doc/macro_reference.qbk` or more readable formats generated from this file.

---

## Comment 6

> Username: jefftrull  
> Created at: 2020-04-13 15:38:59 UTC  
> Url: https://github.com/boostorg/wave/issues/88#issuecomment-612952947  

Looks like the actual macro is awaiting merge approval :) In the meantime I worked out a clang-tidy command line that does this for us:  
  
```  
clang-tidy -header-filter='.*hpp' -checks=-\*,modernize-use-override  -config="{CheckOptions: [{key: modernize-use-override.OverrideSpelling, value: BOOST_OVERRIDE}, {key: modernize-use-override.IgnoreDestructors, value: 1}]}" -p build -fix include/boost/wave/cpp_exceptions.hpp  
```  
  
in case it is useful for others. Caveats:  
  
- Requires a `compile_commands.json` in the "build" directory (change the `-p` option to look elsewhere)  
- `BOOST_OVERRIDE` must already be defined or clang-tidy will silently fail to perform the edit

---

## Comment 7

> Username: EugeneZelenko  
> Created at: 2020-04-13 17:58:51 UTC  
> Updated at: 2020-04-13 22:44:47 UTC  
> Url: https://github.com/boostorg/wave/issues/88#issuecomment-613015575  

`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 8

> Username: jefftrull  
> Created at: 2020-04-13 22:52:54 UTC  
> Url: https://github.com/boostorg/wave/issues/88#issuecomment-613135639  

I found a few more places :)

---

## Comment 9

> Username: EugeneZelenko  
> Created at: 2020-04-13 23:01:02 UTC  
> Url: https://github.com/boostorg/wave/issues/88#issuecomment-613137999  

Great! Thank you for so quick fix!
