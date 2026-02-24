# #104 - GCC suggest-override warnings [Open]

> Username: EugeneZelenko  
> Created at: 2020-04-13 01:46:30 UTC  
> Updated at: 2020-05-26 00:10:11 UTC  
> Url: https://github.com/boostorg/regex/issues/104  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`. Duplicated warnings from same location are omitted:  
  
<pre>  
./boost/regex/v4/cpp_regex_traits.hpp:102:1: warning: ‘std::basic_streambuf<_CharT, _Traits>* boost::re_detail_107200::parser_buf<charT, traits>::setbuf(boost::re_detail_107200::parser_buf<charT, traits>::char_type*, boost::re_detail_107200::parser_buf<charT, traits>::streamsize) [with charT = char; traits = std::char_traits<char>; boost::re_detail_107200::parser_buf<charT, traits>::char_type = char; boost::re_detail_107200::parser_buf<charT, traits>::streamsize = long int]’ can be marked override [-Wsuggest-override]  
./boost/regex/v4/cpp_regex_traits.hpp:110:1: warning: ‘boost::re_detail_107200::parser_buf<charT, traits>::pos_type boost::re_detail_107200::parser_buf<charT, traits>::seekoff(boost::re_detail_107200::parser_buf<charT, traits>::off_type, std::ios_base::seekdir, std::ios_base::openmode) [with charT = char; traits = std::char_traits<char>; boost::re_detail_107200::parser_buf<charT, traits>::pos_type = std::fpos<__mbstate_t>; boost::re_detail_107200::parser_buf<charT, traits>::off_type = long int; std::ios_base::seekdir = std::_Ios_Seekdir; std::ios_base::openmode = std::_Ios_Openmode]’ can be marked override [-Wsuggest-override]  
./boost/regex/v4/cpp_regex_traits.hpp:156:1: warning: ‘boost::re_detail_107200::parser_buf<charT, traits>::pos_type boost::re_detail_107200::parser_buf<charT, traits>::seekpos(boost::re_detail_107200::parser_buf<charT, traits>::pos_type, std::ios_base::openmode) [with charT = char; traits = std::char_traits<char>; boost::re_detail_107200::parser_buf<charT, traits>::pos_type = std::fpos<__mbstate_t>; std::ios_base::openmode = std::_Ios_Openmode]’ can be marked override [-Wsuggest-override]  
</pre>  
  
`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 1

> Username: EugeneZelenko  
> Created at: 2020-05-26 00:10:11 UTC  
> Url: https://github.com/boostorg/regex/issues/104#issuecomment-633748250  

See fixes in #108.
