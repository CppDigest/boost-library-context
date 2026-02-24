# #262 - Exception containing invalid utf-8 [Open]

> Username: BenjaminSchaaf  
> Created at: 2025-10-30 04:03:13 UTC  
> Updated at: 2025-10-30 04:04:59 UTC  
> Url: https://github.com/boostorg/regex/issues/262  

When an invalid regex is provided, `boost::regex` throws an exception with an error message containing the regex pattern. When using a non-utf8 format the exception's `std::string` error message can end up containing invalid utf8.  
  
The problem is on this line: https://github.com/boostorg/regex/blob/ed6ebbd5a52b566e454e0cc34494dd2912c6c66c/include/boost/regex/v5/basic_regex_parser.hpp#L236 If you use say `boost::u32regex`, then `m_base` is a `char32_t *`. Constructing a `std::string` from two `char32_t *` compiles but truncates characters, resulting in invalid utf8.  
  
I'm not sure what the solution here is. In the mean time I've added a function to the character traits for converting a `std::basic_string<charT>` to `std::string`.
