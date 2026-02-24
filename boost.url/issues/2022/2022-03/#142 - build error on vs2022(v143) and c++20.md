# #142 - build error on vs2022(v143) and c++20 [Closed]

> Username: alandefreitas  
> Created at: 2022-03-16 15:09:41 UTC  
> Updated at: 2022-03-16 15:23:05 UTC  
> Closed at: 2022-03-16 15:23:04 UTC  
> Url: https://github.com/boostorg/url/issues/142  

### Discussed in https://github.com/CPPAlliance/url/discussions/141  
  
<div type='discussions-op-text'>  
  
<sup>Originally posted by **itmanfei** March 11, 2022</sup>  
use #include <boost/url/src.hpp>  
  
build output:  
  
1>Please define _WIN32_WINNT or _WIN32_WINDOWS appropriately. For example:  
1>- add -D_WIN32_WINNT=0x0601 to the compiler command line; or  
1>- add _WIN32_WINNT=0x0601 to your project's Preprocessor Definitions.  
1>Assuming _WIN32_WINNT=0x0601 (i.e. Windows 7 target).  
1>boost\url\rfc\impl\paths_rule.ipp(119,1): error C2131: 表达式的计算结果不是常数  
1>boost\url\rfc\impl\paths_rule.ipp(119,1): message : 非常量参数或对非常量符号的引用导致了故障  
1>boost\url\rfc\impl\paths_rule.ipp(119,1): message : 请参见“long `public: static bool __cdecl boost::urls::path_abempty_rule::increment(char const * &,char const *,boost::system::error_code &,boost::urls::pct_encoded_str &)'::`1'::__LINE__Var”的用法  
1>boost\url\rfc\impl\paths_rule.ipp(138,1): error C2131: 表达式的计算结果不是常数  
1>boost\url\rfc\impl\paths_rule.ipp(138,1): message : 非常量参数或对非常量符号的引用导致了故障  
1>boost\url\rfc\impl\paths_rule.ipp(138,1): message : 请参见“long `public: static bool __cdecl boost::urls::path_absolute_rule::begin(char const * &,char const * const,boost::system::error_code &,boost::urls::pct_encoded_str &)'::`1'::__LINE__Var”的用法  
1>boost\url\rfc\impl\paths_rule.ipp(145,1): error C2131: 表达式的计算结果不是常数  
1>boost\url\rfc\impl\paths_rule.ipp(145,1): message : 非常量参数或对非常量符号的引用导致了故障  
1>boost\url\rfc\impl\paths_rule.ipp(145,1): message : 请参见“long `public: static bool __cdecl boost::urls::path_absolute_rule::begin(char const * &,char const * const,boost::system::error_code &,boost::urls::pct_encoded_str &)'::`1'::__LINE__Var”的用法  
1>boost\url\rfc\impl\paths_rule.ipp(155,1): error C2131: 表达式的计算结果不是常数  
1>boost\url\rfc\impl\paths_rule.ipp(155,1): message : 非常量参数或对非常量符号的引用导致了故障  
1>boost\url\rfc\impl\paths_rule.ipp(155,1): message : 请参见“long `public: static bool __cdecl boost::urls::path_absolute_rule::begin(char const * &,char const * const,boost::system::error_code &,boost::urls::pct_encoded_str &)'::`1'::__LINE__Var”的用法  
1>boost\url\rfc\impl\paths_rule.ipp(177,1): error C2131: 表达式的计算结果不是常数  
1>boost\url\rfc\impl\paths_rule.ipp(177,1): message : 非常量参数或对非常量符号的引用导致了故障  
1>boost\url\rfc\impl\paths_rule.ipp(177,1): message : 请参见“long `public: static bool __cdecl boost::urls::path_absolute_rule::increment(char const * &,char const * const,boost::system::error_code &,boost::urls::pct_encoded_str &)'::`1'::__LINE__Var”的用法</div>
