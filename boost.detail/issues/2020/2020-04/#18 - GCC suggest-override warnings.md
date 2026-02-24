# #18 - GCC suggest-override warnings [Open]

> Username: EugeneZelenko  
> Created at: 2020-04-12 19:06:53 UTC  
> Updated at: 2020-04-13 18:02:51 UTC  
> Url: https://github.com/boostorg/detail/issues/18  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`:  
  
<pre>  
./boost/detail/basic_pointerbuf.hpp:123:1: warning: ‘boost::detail::basic_pointerbuf<charT, BufferT>::pos_type boost::detail::basic_pointerbuf<charT, BufferT>::seekpos(boost::detail::basic_pointerbuf<charT, BufferT>::pos_type, std::ios_base::openmode) [with charT = char; BufferT = std::__cxx11::basic_stringbuf<char>; boost::detail::basic_pointerbuf<charT, BufferT>::pos_type = std::fpos<__mbstate_t>; std::ios_base::openmode = std::_Ios_Openmode]’ can be marked override [-Wsuggest-override]  
./boost/detail/basic_pointerbuf.hpp:69:1: warning: ‘BufferT* boost::detail::basic_pointerbuf<charT, BufferT>::setbuf(boost::detail::basic_pointerbuf<charT, BufferT>::char_type*, boost::detail::basic_pointerbuf<charT, BufferT>::streamsize) [with charT = char; BufferT = std::__cxx11::basic_stringbuf<char>; boost::detail::basic_pointerbuf<charT, BufferT>::char_type = char; boost::detail::basic_pointerbuf<charT, BufferT>::streamsize = long int]’ can be marked override [-Wsuggest-override]  
./boost/detail/basic_pointerbuf.hpp:77:1: warning: ‘boost::detail::basic_pointerbuf<charT, BufferT>::pos_type boost::detail::basic_pointerbuf<charT, BufferT>::seekoff(boost::detail::basic_pointerbuf<charT, BufferT>::off_type, std::ios_base::seekdir, std::ios_base::openmode) [with charT = char; BufferT = std::__cxx11::basic_stringbuf<char>; boost::detail::basic_pointerbuf<charT, BufferT>::pos_type = std::fpos<__mbstate_t>; boost::detail::basic_pointerbuf<charT, BufferT>::off_type = long int; std::ios_base::seekdir = std::_Ios_Seekdir; std::ios_base::openmode = std::_Ios_Openmode]’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:119:39: warning: ‘virtual std::codecvt_base::result boost::archive::detail::utf8_codecvt_facet::do_in(mbstate_t&, const char*, const char*, const char*&, wchar_t*, wchar_t*, wchar_t*&) const’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:119:39: warning: ‘virtual std::codecvt_base::result boost::filesystem::detail::utf8_codecvt_facet::do_in(mbstate_t&, const char*, const char*, const char*&, wchar_t*, wchar_t*, wchar_t*&) const’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:119:39: warning: ‘virtual std::codecvt_base::result boost::program_options::detail::utf8_codecvt_facet::do_in(mbstate_t&, const char*, const char*, const char*&, wchar_t*, wchar_t*, wchar_t*&) const’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:129:39: warning: ‘virtual std::codecvt_base::result boost::archive::detail::utf8_codecvt_facet::do_out(mbstate_t&, const wchar_t*, const wchar_t*, const wchar_t*&, char*, char*, char*&) const’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:129:39: warning: ‘virtual std::codecvt_base::result boost::filesystem::detail::utf8_codecvt_facet::do_out(mbstate_t&, const wchar_t*, const wchar_t*, const wchar_t*&, char*, char*, char*&) const’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:129:39: warning: ‘virtual std::codecvt_base::result boost::program_options::detail::utf8_codecvt_facet::do_out(mbstate_t&, const wchar_t*, const wchar_t*, const wchar_t*&, char*, char*, char*&) const’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:159:18: warning: ‘virtual bool boost::archive::detail::utf8_codecvt_facet::do_always_noconv() const’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:159:18: warning: ‘virtual bool boost::filesystem::detail::utf8_codecvt_facet::do_always_noconv() const’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:159:18: warning: ‘virtual bool boost::program_options::detail::utf8_codecvt_facet::do_always_noconv() const’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:164:39: warning: ‘virtual std::codecvt_base::result boost::archive::detail::utf8_codecvt_facet::do_unshift(mbstate_t&, char*, char*, char*&) const’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:164:39: warning: ‘virtual std::codecvt_base::result boost::filesystem::detail::utf8_codecvt_facet::do_unshift(mbstate_t&, char*, char*, char*&) const’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:164:39: warning: ‘virtual std::codecvt_base::result boost::program_options::detail::utf8_codecvt_facet::do_unshift(mbstate_t&, char*, char*, char*&) const’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:174:17: warning: ‘virtual int boost::archive::detail::utf8_codecvt_facet::do_encoding() const’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:174:17: warning: ‘virtual int boost::filesystem::detail::utf8_codecvt_facet::do_encoding() const’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:174:17: warning: ‘virtual int boost::program_options::detail::utf8_codecvt_facet::do_encoding() const’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:181:17: warning: ‘virtual int boost::archive::detail::utf8_codecvt_facet::do_length(mbstate_t&, const char*, const char*, std::size_t) const’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:181:17: warning: ‘virtual int boost::filesystem::detail::utf8_codecvt_facet::do_length(mbstate_t&, const char*, const char*, std::size_t) const’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:181:17: warning: ‘virtual int boost::program_options::detail::utf8_codecvt_facet::do_length(mbstate_t&, const char*, const char*, std::size_t) const’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:212:17: warning: ‘virtual int boost::archive::detail::utf8_codecvt_facet::do_max_length() const’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:212:17: warning: ‘virtual int boost::filesystem::detail::utf8_codecvt_facet::do_max_length() const’ can be marked override [-Wsuggest-override]  
./boost/detail/utf8_codecvt_facet.hpp:212:17: warning: ‘virtual int boost::program_options::detail::utf8_codecvt_facet::do_max_length() const’ can be marked override [-Wsuggest-override]  
</pre>  
  
Related: boostorg/config#253

---

## Comment 1

> Username: Lastique  
> Created at: 2020-04-12 22:01:01 UTC  
> Url: https://github.com/boostorg/detail/issues/18#issuecomment-612682576  

https://github.com/boostorg/config/pull/329 needs to be merged first.

---

## Comment 2

> Username: EugeneZelenko  
> Created at: 2020-04-13 18:02:50 UTC  
> Url: https://github.com/boostorg/detail/issues/18#issuecomment-613017564  

boostorg/config#329 was merged. See also  boostorg/wave#88 for Clang-tidy command line to fix warnings.
