# #93 - GCC suggest-override warnings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-13 01:44:44 UTC  
> Updated at: 2025-09-12 15:21:46 UTC  
> Closed at: 2025-09-12 15:21:46 UTC  
> Url: https://github.com/boostorg/program_options/issues/93  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`:  
  
<pre>  
./boost/program_options/detail/config_file.hpp:167:5: warning: ‘bool boost::program_options::detail::basic_config_file_iterator<charT>::getline(std::__cxx11::string&) [with charT = char; std::__cxx11::string = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
./boost/program_options/detail/config_file.hpp:167:5: warning: ‘bool boost::program_options::detail::basic_config_file_iterator<charT>::getline(std::__cxx11::string&) [with charT = wchar_t; std::__cxx11::string = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
./boost/program_options/detail/value_semantic.hpp:174:5: warning: ‘void boost::program_options::typed_value<T, charT>::xparse(boost::any&, const std::vector<std::__cxx11::basic_string<charT> >&) const [with T = bool; charT = char]’ can be marked override [-Wsuggest-override]  
./boost/program_options/detail/value_semantic.hpp:20:5: warning: ‘std::__cxx11::string boost::program_options::typed_value<T, charT>::name() const [with T = bool; charT = char; std::__cxx11::string = std::__cxx11::basic_string<char>]’ can be marked override [-Wsuggest-override]  
./boost/program_options/detail/value_semantic.hpp:38:5: warning: ‘void boost::program_options::typed_value<T, charT>::notify(const boost::any&) const [with T = bool; charT = char]’ can be marked override [-Wsuggest-override]  
./boost/program_options/errors.hpp:186:29: warning: ‘virtual const char* boost::program_options::error_with_option_name::what() const’ can be marked override [-Wsuggest-override]  
./boost/program_options/errors.hpp:259:22: warning: ‘virtual void boost::program_options::error_with_no_option_name::set_option_name(const string&)’ can be marked override [-Wsuggest-override]  
./boost/program_options/errors.hpp:292:22: warning: ‘virtual void boost::program_options::ambiguous_option::substitute_placeholders(const string&) const’ can be marked override [-Wsuggest-override]  
./boost/program_options/errors.hpp:346:29: warning: ‘virtual std::__cxx11::string boost::program_options::invalid_config_file_syntax::tokens() const’ can be marked override [-Wsuggest-override]  
./boost/program_options/value_semantic.hpp:113:14: warning: ‘virtual void boost::program_options::value_semantic_codecvt_helper<wchar_t>::parse(boost::any&, const std::vector<std::__cxx11::basic_string<char> >&, bool) const’ can be marked override [-Wsuggest-override]  
./boost/program_options/value_semantic.hpp:133:21: warning: ‘virtual std::__cxx11::string boost::program_options::untyped_value::name() const’ can be marked override [-Wsuggest-override]  
./boost/program_options/value_semantic.hpp:135:18: warning: ‘virtual unsigned int boost::program_options::untyped_value::min_tokens() const’ can be marked override [-Wsuggest-override]  
./boost/program_options/value_semantic.hpp:136:18: warning: ‘virtual unsigned int boost::program_options::untyped_value::max_tokens() const’ can be marked override [-Wsuggest-override]  
./boost/program_options/value_semantic.hpp:138:14: warning: ‘virtual bool boost::program_options::untyped_value::is_composing() const’ can be marked override [-Wsuggest-override]  
./boost/program_options/value_semantic.hpp:140:14: warning: ‘virtual bool boost::program_options::untyped_value::is_required() const’ can be marked override [-Wsuggest-override]  
./boost/program_options/value_semantic.hpp:147:14: warning: ‘virtual void boost::program_options::untyped_value::xparse(boost::any&, const std::vector<std::__cxx11::basic_string<char> >&) const’ can be marked override [-Wsuggest-override]  
./boost/program_options/value_semantic.hpp:151:14: warning: ‘virtual bool boost::program_options::untyped_value::apply_default(boost::any&) const’ can be marked override [-Wsuggest-override]  
./boost/program_options/value_semantic.hpp:154:14: warning: ‘virtual void boost::program_options::untyped_value::notify(const boost::any&) const’ can be marked override [-Wsuggest-override]  
./boost/program_options/value_semantic.hpp:304:14: warning: ‘bool boost::program_options::typed_value<T, charT>::is_composing() const [with T = bool; charT = char]’ can be marked override [-Wsuggest-override]  
./boost/program_options/value_semantic.hpp:306:18: warning: ‘unsigned int boost::program_options::typed_value<T, charT>::min_tokens() const [with T = bool; charT = char]’ can be marked override [-Wsuggest-override]  
./boost/program_options/value_semantic.hpp:315:18: warning: ‘unsigned int boost::program_options::typed_value<T, charT>::max_tokens() const [with T = bool; charT = char]’ can be marked override [-Wsuggest-override]  
./boost/program_options/value_semantic.hpp:325:14: warning: ‘bool boost::program_options::typed_value<T, charT>::is_required() const [with T = bool; charT = char]’ can be marked override [-Wsuggest-override]  
./boost/program_options/value_semantic.hpp:337:22: warning: ‘bool boost::program_options::typed_value<T, charT>::apply_default(boost::any&) const [with T = bool; charT = char]’ can be marked override [-Wsuggest-override]  
./boost/program_options/value_semantic.hpp:355:31: warning: ‘const std::type_info& boost::program_options::typed_value<T, charT>::value_type() const [with T = bool; charT = char]’ can be marked override [-Wsuggest-override]  
./boost/program_options/value_semantic.hpp:93:14: warning: ‘virtual void boost::program_options::value_semantic_codecvt_helper<char>::parse(boost::any&, const std::vector<std::__cxx11::basic_string<char> >&, bool) const’ can be marked override [-Wsuggest-override]  
./boost/program_options/variables_map.hpp:165:31: warning: ‘virtual const boost::program_options::variable_value& boost::program_options::variables_map::get(const string&) const’ can be marked override [-Wsuggest-override]  
</pre>  
  
`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 1

> Username: EugeneZelenko  
> Created at: 2020-05-13 16:26:21 UTC  
> Url: https://github.com/boostorg/program_options/issues/93#issuecomment-628101384  

See fixes in  #97.
