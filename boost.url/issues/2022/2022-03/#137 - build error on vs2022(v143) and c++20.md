# #137 - build error on vs2022(v143) and c++20 [Closed]

> Username: segeter  
> Created at: 2022-03-11 03:17:13 UTC  
> Updated at: 2022-03-16 14:47:39 UTC  
> Closed at: 2022-03-16 14:47:39 UTC  
> Url: https://github.com/boostorg/url/issues/137  

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
1>boost\url\rfc\impl\paths_rule.ipp(177,1): message : 请参见“long `public: static bool __cdecl boost::urls::path_absolute_rule::increment(char const * &,char const * const,boost::system::error_code &,boost::urls::pct_encoded_str &)'::`1'::__LINE__Var”的用法

---

## Comment 1

> Username: segeter  
> Created at: 2022-03-11 03:22:11 UTC  
> Url: https://github.com/boostorg/url/issues/137#issuecomment-1064734375  

Maybe i need to define BOOST_URL_NO_SOURCE_LOCATION

---

## Comment 2

> Username: pdimov  
> Created at: 2022-03-11 03:30:50 UTC  
> Url: https://github.com/boostorg/url/issues/137#issuecomment-1064738111  

If you're putting this include in a precompiled header, don't. It needs to be put in exactly one source file.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-03-15 16:57:23 UTC  
> Url: https://github.com/boostorg/url/issues/137#issuecomment-1068224511  

@itmanfei , have you experimented with [these macros](https://develop.url.cpp.al/url/quick_look.html#url.quick_look.headers)?  
  
Does it still fail?

---

## Comment 4

> Username: segeter  
> Created at: 2022-03-16 04:47:42 UTC  
> Url: https://github.com/boostorg/url/issues/137#issuecomment-1068728511  

@alandefreitas  
still fail.  
  
The test method is as follows:   
  
main.cpp  
`  
#define BOOST_URL_NO_LIB  
#include <boost/url/src.hpp>  
int main() {  
  const auto result = boost::urls::parse_uri("");  
}  
`  
  
other_cpp.cpp  
`  
#define BOOST_URL_NO_LIB  
#include <boost/url/src.hpp>  
void test() {  
  const auto result = boost::urls::parse_uri("");  
}  
`  
  
build output:  
已启动重新生成…  
1>------ 已启动全部重新生成: 项目: test_boost_url, 配置: Debug x64 ------  
1>main.cpp  
1>other_cpp.cpp  
1>正在生成代码...  
1>other_cpp.obj : error LNK2005: "private: __cdecl boost::urls::authority_view::authority_view(class boost::urls::authority_view const &,char const *)" (??0authority_view@urls@boost@@AEAA@AEBV012@PEBD@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: __cdecl boost::urls::authority_view::authority_view(char const *)" (??0authority_view@urls@boost@@AEAA@PEBD@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::authority_view::authority_view(class boost::urls::authority_view const &)" (??0authority_view@urls@boost@@QEAA@AEBV012@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::authority_view::authority_view(void)" (??0authority_view@urls@boost@@QEAA@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::detail::basic_copied_strings::basic_copied_strings(class boost::core::basic_string_view<char>,char *,unsigned __int64)" (??0basic_copied_strings@detail@urls@boost@@QEAA@V?$basic_string_view@D@core@3@PEAD_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: __cdecl boost::urls::const_string::const_string(struct boost::urls::const_string::result const &)" (??0const_string@urls@boost@@AEAA@AEBUresult@012@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::const_string::const_string(class boost::urls::const_string const &)" (??0const_string@urls@boost@@QEAA@AEBV012@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::const_string::const_string(void)" (??0const_string@urls@boost@@QEAA@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::detail::enc_path_iter::enc_path_iter(class boost::core::basic_string_view<char>)" (??0enc_path_iter@detail@urls@boost@@QEAA@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::detail::enc_query_iter::enc_query_iter(class boost::core::basic_string_view<char>)" (??0enc_query_iter@detail@urls@boost@@QEAA@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::const_string::factory::factory(class boost::urls::const_string::factory const &)" (??0factory@const_string@urls@boost@@QEAA@AEBV0123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::const_string::factory::factory(void)" (??0factory@const_string@urls@boost@@QEAA@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::ipv4_address::ipv4_address(class std::array<unsigned char,4> const &)" (??0ipv4_address@urls@boost@@QEAA@AEBV?$array@E$03@std@@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::ipv4_address::ipv4_address(unsigned int)" (??0ipv4_address@urls@boost@@QEAA@I@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::ipv4_address::ipv4_address(class boost::core::basic_string_view<char>)" (??0ipv4_address@urls@boost@@QEAA@V?$basic_string_view@D@core@2@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::ipv6_address::ipv6_address(class std::array<unsigned char,16> const &)" (??0ipv6_address@urls@boost@@QEAA@AEBV?$array@E$0BA@@std@@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::ipv6_address::ipv6_address(class boost::urls::ipv4_address const &)" (??0ipv6_address@urls@boost@@QEAA@AEBVipv4_address@12@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::ipv6_address::ipv6_address(class boost::core::basic_string_view<char>)" (??0ipv6_address@urls@boost@@QEAA@V?$basic_string_view@D@core@2@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: __cdecl boost::urls::params_encoded_view::iterator::iterator(class boost::core::basic_string_view<char>)" (??0iterator@params_encoded_view@urls@boost@@AEAA@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: __cdecl boost::urls::params_encoded_view::iterator::iterator(class boost::core::basic_string_view<char>,int)" (??0iterator@params_encoded_view@urls@boost@@AEAA@V?$basic_string_view@D@core@3@H@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: __cdecl boost::urls::params_view::iterator::iterator(class boost::core::basic_string_view<char>,int,class boost::urls::const_string::factory)" (??0iterator@params_view@urls@boost@@AEAA@V?$basic_string_view@D@core@3@HVfactory@const_string@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: __cdecl boost::urls::params_view::iterator::iterator(class boost::core::basic_string_view<char>,class boost::urls::const_string::factory)" (??0iterator@params_view@urls@boost@@AEAA@V?$basic_string_view@D@core@3@Vfactory@const_string@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: __cdecl boost::urls::segments_encoded_view::iterator::iterator(class boost::core::basic_string_view<char>,unsigned __int64)" (??0iterator@segments_encoded_view@urls@boost@@AEAA@V?$basic_string_view@D@core@3@_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: __cdecl boost::urls::segments_encoded_view::iterator::iterator(class boost::core::basic_string_view<char>,unsigned __int64,int)" (??0iterator@segments_encoded_view@urls@boost@@AEAA@V?$basic_string_view@D@core@3@_KH@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: __cdecl boost::urls::segments_view::iterator::iterator(class boost::core::basic_string_view<char>,unsigned __int64,class boost::urls::const_string::factory const &)" (??0iterator@segments_view@urls@boost@@AEAA@V?$basic_string_view@D@core@3@_KAEBVfactory@const_string@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: __cdecl boost::urls::segments_view::iterator::iterator(class boost::core::basic_string_view<char>,unsigned __int64,class boost::urls::const_string::factory const &,int)" (??0iterator@segments_view@urls@boost@@AEAA@V?$basic_string_view@D@core@3@_KAEBVfactory@const_string@23@H@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::segments_view::iterator::iterator(class boost::urls::segments_view::iterator const &)" (??0iterator@segments_view@urls@boost@@QEAA@AEBV0123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::segments_view::iterator::iterator(void)" (??0iterator@segments_view@urls@boost@@QEAA@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::detail::plain_path_iter::plain_path_iter(class boost::core::basic_string_view<char>)" (??0plain_path_iter@detail@urls@boost@@QEAA@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::detail::plain_query_iter::plain_query_iter(class boost::core::basic_string_view<char>)" (??0plain_query_iter@detail@urls@boost@@QEAA@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: __cdecl boost::urls::params::reference::reference(char const *,unsigned __int64,unsigned __int64,class boost::urls::const_string::factory const &)" (??0reference@params@urls@boost@@AEAA@PEBD_K1AEBVfactory@const_string@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: __cdecl boost::urls::segments_encoded_view::segments_encoded_view(class boost::core::basic_string_view<char>,unsigned __int64)" (??0segments_encoded_view@urls@boost@@AEAA@V?$basic_string_view@D@core@2@_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: __cdecl boost::urls::static_url_base::static_url_base(char *,unsigned __int64)" (??0static_url_base@urls@boost@@IEAA@PEAD_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: __cdecl boost::urls::url::url(char *,unsigned __int64)" (??0url@urls@boost@@IEAA@PEAD_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::url::url(class boost::urls::url &&)" (??0url@urls@boost@@QEAA@$$QEAV012@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::url::url(class boost::urls::url const &)" (??0url@urls@boost@@QEAA@AEBV012@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::url::url(class boost::urls::url_view const &)" (??0url@urls@boost@@QEAA@AEBVurl_view@12@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::url::url(class boost::core::basic_string_view<char>)" (??0url@urls@boost@@QEAA@V?$basic_string_view@D@core@2@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::url::url(void)" (??0url@urls@boost@@QEAA@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: __cdecl boost::urls::url_view::url_view(class boost::urls::url_view const &,char const *)" (??0url_view@urls@boost@@IEAA@AEBV012@PEBD@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: __cdecl boost::urls::url_view::url_view(int,char const *)" (??0url_view@urls@boost@@IEAA@HPEBD@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::url_view::url_view(class boost::urls::url_view const &)" (??0url_view@urls@boost@@QEAA@AEBV012@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::url_view::url_view(class boost::core::basic_string_view<char>)" (??0url_view@urls@boost@@QEAA@V?$basic_string_view@D@core@2@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::url_view::url_view(void)" (??0url_view@urls@boost@@QEAA@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: __cdecl boost::urls::params_view::value_type::value_type(char const *,unsigned __int64,unsigned __int64,class boost::urls::const_string::factory const &)" (??0value_type@params_view@urls@boost@@AEAA@PEBD_K1AEBVfactory@const_string@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::params_view::value_type::value_type(class boost::urls::params_view::value_type const &)" (??0value_type@params_view@urls@boost@@QEAA@AEBV0123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::params_view::value_type::value_type(void)" (??0value_type@params_view@urls@boost@@QEAA@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: virtual __cdecl boost::urls::detail::any_path_iter::~any_path_iter(void)" (??1any_path_iter@detail@urls@boost@@UEAA@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: virtual __cdecl boost::urls::detail::any_query_iter::~any_query_iter(void)" (??1any_query_iter@detail@urls@boost@@UEAA@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: virtual __cdecl boost::urls::authority_view::~authority_view(void)" (??1authority_view@urls@boost@@UEAA@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::detail::basic_copied_strings::~basic_copied_strings(void)" (??1basic_copied_strings@detail@urls@boost@@QEAA@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::basic_static_pool::~basic_static_pool(void)" (??1basic_static_pool@urls@boost@@QEAA@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::const_string::~const_string(void)" (??1const_string@urls@boost@@QEAA@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::const_string::factory::~factory(void)" (??1factory@const_string@urls@boost@@QEAA@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: virtual __cdecl boost::urls::static_url_base::~static_url_base(void)" (??1static_url_base@urls@boost@@MEAA@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: virtual __cdecl boost::urls::url::~url(void)" (??1url@urls@boost@@UEAA@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: virtual __cdecl boost::urls::url_view::~url_view(void)" (??1url_view@urls@boost@@UEAA@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: __cdecl boost::urls::params_view::value_type::~value_type(void)" (??1value_type@params_view@urls@boost@@QEAA@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::authority_view & __cdecl boost::urls::authority_view::operator=(class boost::urls::authority_view const &)" (??4authority_view@urls@boost@@QEAAAEAV012@AEBV012@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::const_string & __cdecl boost::urls::const_string::operator=(class boost::urls::const_string const &)" (??4const_string@urls@boost@@QEAAAEAV012@AEBV012@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::const_string::factory & __cdecl boost::urls::const_string::factory::operator=(class boost::urls::const_string::factory const &)" (??4factory@const_string@urls@boost@@QEAAAEAV0123@AEBV0123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::segments_view::iterator & __cdecl boost::urls::segments_view::iterator::operator=(class boost::urls::segments_view::iterator const &)" (??4iterator@segments_view@urls@boost@@QEAAAEAV0123@AEBV0123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::operator=(class boost::urls::url &&)" (??4url@urls@boost@@QEAAAEAV012@$$QEAV012@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::operator=(class boost::urls::url_view const &)" (??4url@urls@boost@@QEAAAEAV012@AEBVurl_view@12@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url_view & __cdecl boost::urls::url_view::operator=(class boost::urls::url_view const &)" (??4url_view@urls@boost@@QEAAAEAV012@AEBV012@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params_view::value_type & __cdecl boost::urls::params_view::value_type::operator=(class boost::urls::params_view::value_type const &)" (??4value_type@params_view@urls@boost@@QEAAAEAV0123@AEBV0123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class std::basic_ostream<char,struct std::char_traits<char> > & __cdecl boost::urls::operator<<(class std::basic_ostream<char,struct std::char_traits<char> > &,class boost::urls::authority_view const &)" (??6urls@boost@@YAAEAV?$basic_ostream@DU?$char_traits@D@std@@@std@@AEAV23@AEBVauthority_view@01@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class std::basic_ostream<char,struct std::char_traits<char> > & __cdecl boost::urls::operator<<(class std::basic_ostream<char,struct std::char_traits<char> > &,class boost::urls::ipv4_address const &)" (??6urls@boost@@YAAEAV?$basic_ostream@DU?$char_traits@D@std@@@std@@AEAV23@AEBVipv4_address@01@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class std::basic_ostream<char,struct std::char_traits<char> > & __cdecl boost::urls::operator<<(class std::basic_ostream<char,struct std::char_traits<char> > &,class boost::urls::ipv6_address const &)" (??6urls@boost@@YAAEAV?$basic_ostream@DU?$char_traits@D@std@@@std@@AEAV23@AEBVipv6_address@01@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class std::basic_ostream<char,struct std::char_traits<char> > & __cdecl boost::urls::operator<<(class std::basic_ostream<char,struct std::char_traits<char> > &,class boost::urls::segments_encoded_view const &)" (??6urls@boost@@YAAEAV?$basic_ostream@DU?$char_traits@D@std@@@std@@AEAV23@AEBVsegments_encoded_view@01@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class std::basic_ostream<char,struct std::char_traits<char> > & __cdecl boost::urls::operator<<(class std::basic_ostream<char,struct std::char_traits<char> > &,class boost::urls::segments_view const &)" (??6urls@boost@@YAAEAV?$basic_ostream@DU?$char_traits@D@std@@@std@@AEAV23@AEBVsegments_view@01@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class std::basic_ostream<char,struct std::char_traits<char> > & __cdecl boost::urls::operator<<(class std::basic_ostream<char,struct std::char_traits<char> > &,class boost::urls::url const &)" (??6urls@boost@@YAAEAV?$basic_ostream@DU?$char_traits@D@std@@@std@@AEAV23@AEBVurl@01@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class std::basic_ostream<char,struct std::char_traits<char> > & __cdecl boost::urls::operator<<(class std::basic_ostream<char,struct std::char_traits<char> > &,class boost::urls::url_view const &)" (??6urls@boost@@YAAEAV?$basic_ostream@DU?$char_traits@D@std@@@std@@AEAV23@AEBVurl_view@01@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "bool __cdecl boost::urls::operator==(class boost::urls::ipv6_address const &,class boost::urls::ipv6_address const &)" (??8urls@boost@@YA_NAEBVipv6_address@01@0@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "bool __cdecl boost::urls::operator==(class boost::urls::params_encoded_view::iterator,class boost::urls::params_encoded_view::iterator)" (??8urls@boost@@YA_NViterator@params_encoded_view@01@0@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "bool __cdecl boost::urls::operator==(class boost::urls::params_view::iterator,class boost::urls::params_view::iterator)" (??8urls@boost@@YA_NViterator@params_view@01@0@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params::reference __cdecl boost::urls::params::operator[](unsigned __int64)const " (??Aparams@urls@boost@@QEBA?AVreference@012@_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: struct boost::urls::query_param_view __cdecl boost::urls::params_encoded::operator[](unsigned __int64)const " (??Aparams_encoded@urls@boost@@QEBA?AUquery_param_view@12@_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::const_string __cdecl boost::urls::segments::operator[](unsigned __int64)const " (??Asegments@urls@boost@@QEBA?AVconst_string@12@_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::segments_encoded::operator[](unsigned __int64)const " (??Asegments_encoded@urls@boost@@QEBA?AV?$basic_string_view@D@core@2@_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params::reference __cdecl boost::urls::params::iterator::operator*(void)const " (??Diterator@params@urls@boost@@QEBA?AVreference@123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: struct boost::urls::query_param_view __cdecl boost::urls::params_encoded::iterator::operator*(void)const " (??Diterator@params_encoded@urls@boost@@QEBA?AUquery_param_view@23@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: struct boost::urls::query_param_view __cdecl boost::urls::params_encoded_view::iterator::operator*(void)const " (??Diterator@params_encoded_view@urls@boost@@QEBA?AUquery_param_view@23@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params_view::value_type __cdecl boost::urls::params_view::iterator::operator*(void)const " (??Diterator@params_view@urls@boost@@QEBA?AVvalue_type@123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::const_string __cdecl boost::urls::segments::iterator::operator*(void)const " (??Diterator@segments@urls@boost@@QEBA?AVconst_string@23@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::segments_encoded::iterator::operator*(void)const " (??Diterator@segments_encoded@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::const_string __cdecl boost::urls::segments_view::iterator::operator*(void)const " (??Diterator@segments_view@urls@boost@@QEBA?AVconst_string@23@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params_encoded_view::iterator & __cdecl boost::urls::params_encoded_view::iterator::operator++(void)" (??Eiterator@params_encoded_view@urls@boost@@QEAAAEAV0123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params_view::iterator & __cdecl boost::urls::params_view::iterator::operator++(void)" (??Eiterator@params_view@urls@boost@@QEAAAEAV0123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::segments_encoded_view::iterator & __cdecl boost::urls::segments_encoded_view::iterator::operator++(void)" (??Eiterator@segments_encoded_view@urls@boost@@QEAAAEAV0123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::segments_view::iterator & __cdecl boost::urls::segments_view::iterator::operator++(void)" (??Eiterator@segments_view@urls@boost@@QEAAAEAV0123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::segments_encoded_view::iterator & __cdecl boost::urls::segments_encoded_view::iterator::operator--(void)" (??Fiterator@segments_encoded_view@urls@boost@@QEAAAEAV0123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::segments_view::iterator & __cdecl boost::urls::segments_view::iterator::operator--(void)" (??Fiterator@segments_view@urls@boost@@QEAAAEAV0123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::const_string __cdecl boost::urls::const_string::factory::operator()(class boost::core::basic_string_view<char>)const " (??Rfactory@const_string@urls@boost@@QEBA?AV123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void * __cdecl boost::urls::basic_static_pool::allocate(unsigned __int64,unsigned __int64)" (?allocate@basic_static_pool@urls@boost@@AEAAPEAX_K0@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: virtual char * __cdecl boost::urls::static_url_base::allocate(unsigned __int64)" (?allocate@static_url_base@urls@boost@@MEAAPEAD_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: virtual char * __cdecl boost::urls::url::allocate(unsigned __int64)" (?allocate@url@urls@boost@@MEAAPEAD_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::authority_view::apply(struct boost::urls::authority_rule const &)" (?apply@authority_view@urls@boost@@AEAAXAEBUauthority_rule@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::authority_view::apply(struct boost::urls::host_rule const &)" (?apply@authority_view@urls@boost@@AEAAXAEBUhost_rule@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::url_view::apply(struct boost::urls::authority_rule const &)" (?apply@url_view@urls@boost@@AEAAXAEBUauthority_rule@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::url_view::apply(struct boost::urls::fragment_part_rule const &)" (?apply@url_view@urls@boost@@AEAAXAEBUfragment_part_rule@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::url_view::apply(struct boost::urls::host_rule const &)" (?apply@url_view@urls@boost@@AEAAXAEBUhost_rule@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::url_view::apply(struct boost::urls::parsed_path const &)" (?apply@url_view@urls@boost@@AEAAXAEBUparsed_path@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::url_view::apply(struct boost::urls::query_part_rule const &)" (?apply@url_view@urls@boost@@AEAAXAEBUquery_part_rule@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::url_view::apply(struct boost::urls::scheme_part_rule const &)" (?apply@url_view@urls@boost@@AEAAXAEBUscheme_part_rule@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::const_string __cdecl boost::urls::params::at(class boost::core::basic_string_view<char>)const " (?at@params@urls@boost@@QEBA?AVconst_string@23@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::params_encoded::at(class boost::core::basic_string_view<char>)const " (?at@params_encoded@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@V453@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::params_encoded_view::at(class boost::core::basic_string_view<char>)const " (?at@params_encoded_view@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@V453@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::const_string __cdecl boost::urls::params_view::at(class boost::core::basic_string_view<char>)const " (?at@params_view@urls@boost@@QEBA?AVconst_string@23@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params_encoded_view::iterator __cdecl boost::urls::params_encoded_view::begin(void)const " (?begin@params_encoded_view@urls@boost@@QEBA?AViterator@123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params_view::iterator __cdecl boost::urls::params_view::begin(void)const " (?begin@params_view@urls@boost@@QEBA?AViterator@123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: static bool __cdecl boost::urls::path_abempty_rule::begin(char const * &,char const *,class boost::system::error_code &,struct boost::urls::pct_encoded_str &)" (?begin@path_abempty_rule@urls@boost@@SA_NAEAPEBDPEBDAEAVerror_code@system@3@AEAUpct_encoded_str@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: static bool __cdecl boost::urls::path_absolute_rule::begin(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::pct_encoded_str &)" (?begin@path_absolute_rule@urls@boost@@SA_NAEAPEBDQEBDAEAVerror_code@system@3@AEAUpct_encoded_str@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: static bool __cdecl boost::urls::path_noscheme_rule::begin(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::pct_encoded_str &)" (?begin@path_noscheme_rule@urls@boost@@SA_NAEAPEBDQEBDAEAVerror_code@system@3@AEAUpct_encoded_str@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: static bool __cdecl boost::urls::path_rootless_rule::begin(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::pct_encoded_str &)" (?begin@path_rootless_rule@urls@boost@@SA_NAEAPEBDQEBDAEAVerror_code@system@3@AEAUpct_encoded_str@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: static bool __cdecl boost::urls::query_rule::begin(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::query_param_view &)" (?begin@query_rule@urls@boost@@SA_NAEAPEBDQEBDAEAVerror_code@system@3@AEAUquery_param_view@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::segments_encoded_view::iterator __cdecl boost::urls::segments_encoded_view::begin(void)const " (?begin@segments_encoded_view@urls@boost@@QEBA?AViterator@123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::segments_view::iterator __cdecl boost::urls::segments_view::begin(void)const " (?begin@segments_view@urls@boost@@QEBA?AViterator@123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::url::build_tab(void)" (?build_tab@url@urls@boost@@AEAAXXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::url::check_invariants(void)const " (?check_invariants@url@urls@boost@@AEBAXXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: void __cdecl boost::urls::url::clear(void)" (?clear@url@urls@boost@@QEAAXXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class std::shared_ptr<class boost::urls::authority_view const > __cdecl boost::urls::authority_view::collect(void)const " (?collect@authority_view@urls@boost@@QEBA?AV?$shared_ptr@$$CBVauthority_view@urls@boost@@@std@@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class std::shared_ptr<class boost::urls::url_view const > __cdecl boost::urls::url_view::collect(void)const " (?collect@url_view@urls@boost@@QEBA?AV?$shared_ptr@$$CBVurl_view@urls@boost@@@std@@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: void __cdecl boost::urls::static_url_base::construct(class boost::core::basic_string_view<char>)" (?construct@static_url_base@urls@boost@@IEAAXV?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: class boost::urls::url_view __cdecl boost::urls::static_url_base::convert(void)const " (?convert@static_url_base@urls@boost@@IEBA?AVurl_view@23@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: virtual void __cdecl boost::urls::detail::enc_path_iter::copy(char * &,char const *)" (?copy@enc_path_iter@detail@urls@boost@@UEAAXAEAPEADPEBD@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: virtual void __cdecl boost::urls::detail::enc_query_iter::copy(char * &,char const *)" (?copy@enc_query_iter@detail@urls@boost@@UEAAXAEAPEADPEBD@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: virtual void __cdecl boost::urls::detail::plain_path_iter::copy(char * &,char const *)" (?copy@plain_path_iter@detail@urls@boost@@UEAAXAEAPEADPEBD@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: virtual void __cdecl boost::urls::detail::plain_query_iter::copy(char * &,char const *)" (?copy@plain_query_iter@detail@urls@boost@@UEAAXAEAPEADPEBD@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: void __cdecl boost::urls::static_url_base::copy(class boost::urls::url const &)" (?copy@static_url_base@urls@boost@@IEAAXAEBVurl@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: void __cdecl boost::urls::static_url_base::copy(class boost::urls::url_view const &)" (?copy@static_url_base@urls@boost@@IEAAXAEBVurl_view@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: void __cdecl boost::urls::url::copy(class boost::urls::url_view const &)" (?copy@url@urls@boost@@IEAAXAEBVurl_view@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: static void __cdecl boost::urls::detail::enc_params_iter_base::copy_impl(class boost::core::basic_string_view<char>,class boost::core::basic_string_view<char> const *,char * &,char const *)" (?copy_impl@enc_params_iter_base@detail@urls@boost@@KAXV?$basic_string_view@D@core@4@PEBV564@AEAPEADPEBD@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: static void __cdecl boost::urls::detail::enc_segs_iter_base::copy_impl(class boost::core::basic_string_view<char>,char * &,char const *)" (?copy_impl@enc_segs_iter_base@detail@urls@boost@@KAXV?$basic_string_view@D@core@4@AEAPEADPEBD@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: static void __cdecl boost::urls::detail::plain_params_iter_base::copy_impl(class boost::core::basic_string_view<char>,class boost::core::basic_string_view<char> const *,char * &,char const *)" (?copy_impl@plain_params_iter_base@detail@urls@boost@@KAXV?$basic_string_view@D@core@4@PEBV564@AEAPEADPEBD@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: static void __cdecl boost::urls::detail::plain_segs_iter_base::copy_impl(class boost::core::basic_string_view<char>,char * &,char const *)" (?copy_impl@plain_segs_iter_base@detail@urls@boost@@KAXV?$basic_string_view@D@core@4@AEAPEADPEBD@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: unsigned __int64 __cdecl boost::urls::params::count(class boost::core::basic_string_view<char>)const " (?count@params@urls@boost@@QEBA_KV?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: unsigned __int64 __cdecl boost::urls::params_encoded::count(class boost::core::basic_string_view<char>)const " (?count@params_encoded@urls@boost@@QEBA_KV?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: unsigned __int64 __cdecl boost::urls::params_encoded_view::count(class boost::core::basic_string_view<char>)const " (?count@params_encoded_view@urls@boost@@QEBA_KV?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: unsigned __int64 __cdecl boost::urls::params_view::count(class boost::core::basic_string_view<char>)const " (?count@params_view@urls@boost@@QEBA_KV?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::basic_static_pool::deallocate(void *,unsigned __int64,unsigned __int64)" (?deallocate@basic_static_pool@urls@boost@@AEAAXPEAX_K1@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: virtual void __cdecl boost::urls::static_url_base::deallocate(char *)" (?deallocate@static_url_base@urls@boost@@MEAAXPEAD@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: virtual void __cdecl boost::urls::url::deallocate(char *)" (?deallocate@url@urls@boost@@MEAAXPEAD@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: char * __cdecl boost::urls::url::edit_params(unsigned __int64,unsigned __int64,unsigned __int64,unsigned __int64)" (?edit_params@url@urls@boost@@AEAAPEAD_K000@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::url::edit_params(unsigned __int64,unsigned __int64,struct boost::urls::detail::any_query_iter &&,struct boost::urls::detail::any_query_iter &&,bool)" (?edit_params@url@urls@boost@@AEAAX_K0$$QEAUany_query_iter@detail@23@1_N@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: char * __cdecl boost::urls::url::edit_segments(unsigned __int64,unsigned __int64,unsigned __int64,unsigned __int64)" (?edit_segments@url@urls@boost@@AEAAPEAD_K000@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::url::edit_segments(unsigned __int64,unsigned __int64,struct boost::urls::detail::any_path_iter &&,struct boost::urls::detail::any_path_iter &&,int)" (?edit_segments@url@urls@boost@@AEAAX_K0$$QEAUany_path_iter@detail@23@1H@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::url_view::encoded_authority(void)const " (?encoded_authority@url_view@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::url_view::encoded_fragment(void)const " (?encoded_fragment@url_view@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::authority_view::encoded_host(void)const " (?encoded_host@authority_view@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::url_view::encoded_host(void)const " (?encoded_host@url_view@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::authority_view::encoded_host_and_port(void)const " (?encoded_host_and_port@authority_view@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::url_view::encoded_host_and_port(void)const " (?encoded_host_and_port@url_view@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: class boost::core::basic_string_view<char> __cdecl boost::urls::params_encoded_view::iterator::encoded_key(void)const " (?encoded_key@iterator@params_encoded_view@urls@boost@@AEBA?AV?$basic_string_view@D@core@4@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: class boost::core::basic_string_view<char> __cdecl boost::urls::params_view::iterator::encoded_key(void)const " (?encoded_key@iterator@params_view@urls@boost@@AEBA?AV?$basic_string_view@D@core@4@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::url_view::encoded_origin(void)const " (?encoded_origin@url_view@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params_encoded_view __cdecl boost::urls::url_view::encoded_params(void)const " (?encoded_params@url_view@urls@boost@@QEBA?AVparams_encoded_view@23@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::authority_view::encoded_password(void)const " (?encoded_password@authority_view@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::url_view::encoded_password(void)const " (?encoded_password@url_view@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::url_view::encoded_query(void)const " (?encoded_query@url_view@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::segments_encoded __cdecl boost::urls::url::encoded_segments(void)" (?encoded_segments@url@urls@boost@@QEAA?AVsegments_encoded@23@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::url_view::encoded_user(void)const " (?encoded_user@url_view@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::authority_view::encoded_userinfo(void)const " (?encoded_userinfo@authority_view@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::url_view::encoded_userinfo(void)const " (?encoded_userinfo@url_view@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params_encoded_view::iterator __cdecl boost::urls::params_encoded_view::end(void)const " (?end@params_encoded_view@urls@boost@@QEBA?AViterator@123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params_view::iterator __cdecl boost::urls::params_view::end(void)const " (?end@params_view@urls@boost@@QEBA?AViterator@123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::segments_encoded_view::iterator __cdecl boost::urls::segments_encoded_view::end(void)const " (?end@segments_encoded_view@urls@boost@@QEBA?AViterator@123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::segments_view::iterator __cdecl boost::urls::segments_view::end(void)const " (?end@segments_view@urls@boost@@QEBA?AViterator@123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::url::ensure_space(unsigned __int64,unsigned __int64,unsigned __int64)" (?ensure_space@url@urls@boost@@AEAAX_K00@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params::iterator __cdecl boost::urls::params::erase(class boost::urls::params::iterator,class boost::urls::params::iterator)" (?erase@params@urls@boost@@QEAA?AViterator@123@V4123@0@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: unsigned __int64 __cdecl boost::urls::params::erase(class boost::core::basic_string_view<char>)" (?erase@params@urls@boost@@QEAA_KV?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params_encoded::iterator __cdecl boost::urls::params_encoded::erase(class boost::urls::params_encoded::iterator,class boost::urls::params_encoded::iterator)" (?erase@params_encoded@urls@boost@@QEAA?AViterator@123@V4123@0@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: unsigned __int64 __cdecl boost::urls::params_encoded::erase(class boost::core::basic_string_view<char>)" (?erase@params_encoded@urls@boost@@QEAA_KV?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::segments::iterator __cdecl boost::urls::segments::erase(class boost::urls::segments::iterator,class boost::urls::segments::iterator)" (?erase@segments@urls@boost@@QEAA?AViterator@123@V4123@0@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::segments_encoded::iterator __cdecl boost::urls::segments_encoded::erase(class boost::urls::segments_encoded::iterator,class boost::urls::segments_encoded::iterator)" (?erase@segments_encoded@urls@boost@@QEAA?AViterator@123@V4123@0@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void * * __cdecl boost::urls::basic_static_pool::find(void *)" (?find@basic_static_pool@urls@boost@@AEAAPEAPEAXPEAX@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params::iterator __cdecl boost::urls::params::find(class boost::urls::params::iterator,class boost::core::basic_string_view<char>)const " (?find@params@urls@boost@@QEBA?AViterator@123@V4123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params_encoded::iterator __cdecl boost::urls::params_encoded::find(class boost::urls::params_encoded::iterator,class boost::core::basic_string_view<char>)const " (?find@params_encoded@urls@boost@@QEBA?AViterator@123@V4123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params_encoded_view::iterator __cdecl boost::urls::params_encoded_view::find(class boost::urls::params_encoded_view::iterator,class boost::core::basic_string_view<char>)const " (?find@params_encoded_view@urls@boost@@QEBA?AViterator@123@V4123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params_view::iterator __cdecl boost::urls::params_view::find(class boost::urls::params_view::iterator,class boost::core::basic_string_view<char>)const " (?find@params_view@urls@boost@@QEBA?AViterator@123@V4123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: bool __cdecl boost::urls::url_view::has_fragment(void)const " (?has_fragment@url_view@urls@boost@@QEBA_NXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: bool __cdecl boost::urls::authority_view::has_password(void)const " (?has_password@authority_view@urls@boost@@QEBA_NXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: bool __cdecl boost::urls::url_view::has_password(void)const " (?has_password@url_view@urls@boost@@QEBA_NXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: bool __cdecl boost::urls::authority_view::has_port(void)const " (?has_port@authority_view@urls@boost@@QEBA_NXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: bool __cdecl boost::urls::url_view::has_port(void)const " (?has_port@url_view@urls@boost@@QEBA_NXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: bool __cdecl boost::urls::url_view::has_query(void)const " (?has_query@url_view@urls@boost@@QEBA_NXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: bool __cdecl boost::urls::url_view::has_scheme(void)const " (?has_scheme@url_view@urls@boost@@QEBA_NXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: bool __cdecl boost::urls::authority_view::has_userinfo(void)const " (?has_userinfo@authority_view@urls@boost@@QEBA_NXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: bool __cdecl boost::urls::url_view::has_userinfo(void)const " (?has_userinfo@url_view@urls@boost@@QEBA_NXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::detail::enc_path_iter::increment(void)" (?increment@enc_path_iter@detail@urls@boost@@AEAAXXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::detail::enc_query_iter::increment(void)" (?increment@enc_query_iter@detail@urls@boost@@AEAAXXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: static bool __cdecl boost::urls::path_abempty_rule::increment(char const * &,char const *,class boost::system::error_code &,struct boost::urls::pct_encoded_str &)" (?increment@path_abempty_rule@urls@boost@@SA_NAEAPEBDPEBDAEAVerror_code@system@3@AEAUpct_encoded_str@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: static bool __cdecl boost::urls::path_absolute_rule::increment(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::pct_encoded_str &)" (?increment@path_absolute_rule@urls@boost@@SA_NAEAPEBDQEBDAEAVerror_code@system@3@AEAUpct_encoded_str@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: static bool __cdecl boost::urls::path_noscheme_rule::increment(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::pct_encoded_str &)" (?increment@path_noscheme_rule@urls@boost@@SA_NAEAPEBDQEBDAEAVerror_code@system@3@AEAUpct_encoded_str@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: static bool __cdecl boost::urls::path_rootless_rule::increment(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::pct_encoded_str &)" (?increment@path_rootless_rule@urls@boost@@SA_NAEAPEBDQEBDAEAVerror_code@system@3@AEAUpct_encoded_str@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::detail::plain_path_iter::increment(void)" (?increment@plain_path_iter@detail@urls@boost@@AEAAXXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::detail::plain_query_iter::increment(void)" (?increment@plain_query_iter@detail@urls@boost@@AEAAXXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: static bool __cdecl boost::urls::query_rule::increment(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::query_param_view &)" (?increment@query_rule@urls@boost@@SA_NAEAPEBDQEBDAEAVerror_code@system@3@AEAUquery_param_view@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::segments::iterator __cdecl boost::urls::segments::insert(class boost::urls::segments::iterator,class boost::core::basic_string_view<char>)" (?insert@segments@urls@boost@@QEAA?AViterator@123@V4123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::segments_encoded::iterator __cdecl boost::urls::segments_encoded::insert(class boost::urls::segments_encoded::iterator,class boost::core::basic_string_view<char>)" (?insert@segments_encoded@urls@boost@@QEAA?AViterator@123@V4123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::ipv4_address __cdecl boost::urls::authority_view::ipv4_address(void)const " (?ipv4_address@authority_view@urls@boost@@QEBA?AV023@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::ipv4_address __cdecl boost::urls::url_view::ipv4_address(void)const " (?ipv4_address@url_view@urls@boost@@QEBA?AV023@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::ipv6_address __cdecl boost::urls::authority_view::ipv6_address(void)const " (?ipv6_address@authority_view@urls@boost@@QEBA?AV023@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::ipv6_address __cdecl boost::urls::url_view::ipv6_address(void)const " (?ipv6_address@url_view@urls@boost@@QEBA?AV023@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::authority_view::ipv_future(void)const " (?ipv_future@authority_view@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::url_view::ipv_future(void)const " (?ipv_future@url_view@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: bool __cdecl boost::urls::ipv4_address::is_loopback(void)const " (?is_loopback@ipv4_address@urls@boost@@QEBA_NXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: bool __cdecl boost::urls::ipv6_address::is_loopback(void)const " (?is_loopback@ipv6_address@urls@boost@@QEBA_NXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: bool __cdecl boost::urls::ipv4_address::is_multicast(void)const " (?is_multicast@ipv4_address@urls@boost@@QEBA_NXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: bool __cdecl boost::urls::detail::basic_copied_strings::is_overlapping(class boost::core::basic_string_view<char>)const " (?is_overlapping@basic_copied_strings@detail@urls@boost@@AEBA_NV?$basic_string_view@D@core@4@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: bool __cdecl boost::urls::ipv4_address::is_unspecified(void)const " (?is_unspecified@ipv4_address@urls@boost@@QEBA_NXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: bool __cdecl boost::urls::ipv6_address::is_unspecified(void)const " (?is_unspecified@ipv6_address@urls@boost@@QEBA_NXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: bool __cdecl boost::urls::ipv6_address::is_v4_mapped(void)const " (?is_v4_mapped@ipv6_address@urls@boost@@QEBA_NXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "bool __cdecl boost::urls::detail::key_equal_encoded(class boost::core::basic_string_view<char>,class boost::core::basic_string_view<char>)" (?key_equal_encoded@detail@urls@boost@@YA_NV?$basic_string_view@D@core@3@0@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "bool __cdecl boost::urls::detail::key_equal_encoded(class boost::core::basic_string_view<char>,struct boost::urls::pct_encoded_str)" (?key_equal_encoded@detail@urls@boost@@YA_NV?$basic_string_view@D@core@3@Upct_encoded_str@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: static class boost::urls::ipv6_address __cdecl boost::urls::ipv6_address::loopback(void)" (?loopback@ipv6_address@urls@boost@@SA?AV123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class boost::system::error_code __cdecl boost::urls::grammar::make_error_code(enum boost::urls::grammar::error)" (?make_error_code@grammar@urls@boost@@YA?AVerror_code@system@3@W4error@123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class boost::system::error_code __cdecl boost::urls::make_error_code(enum boost::urls::error)" (?make_error_code@urls@boost@@YA?AVerror_code@system@2@W4error@12@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class boost::system::error_condition __cdecl boost::urls::grammar::make_error_condition(enum boost::urls::grammar::condition)" (?make_error_condition@grammar@urls@boost@@YA?AVerror_condition@system@3@W4condition@123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::detail::basic_copied_strings::maybe_copy(class boost::core::basic_string_view<char>)" (?maybe_copy@basic_copied_strings@detail@urls@boost@@QEAA?AV?$basic_string_view@D@core@4@V564@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: virtual bool __cdecl boost::urls::detail::enc_path_iter::measure(unsigned __int64 &,class boost::system::error_code &)" (?measure@enc_path_iter@detail@urls@boost@@UEAA_NAEA_KAEAVerror_code@system@4@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: virtual bool __cdecl boost::urls::detail::enc_query_iter::measure(unsigned __int64 &,class boost::system::error_code &)" (?measure@enc_query_iter@detail@urls@boost@@UEAA_NAEA_KAEAVerror_code@system@4@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: virtual bool __cdecl boost::urls::detail::plain_path_iter::measure(unsigned __int64 &,class boost::system::error_code &)" (?measure@plain_path_iter@detail@urls@boost@@UEAA_NAEA_KAEAVerror_code@system@4@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: virtual bool __cdecl boost::urls::detail::plain_query_iter::measure(unsigned __int64 &,class boost::system::error_code &)" (?measure@plain_query_iter@detail@urls@boost@@UEAA_NAEA_KAEAVerror_code@system@4@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: static bool __cdecl boost::urls::detail::enc_params_iter_base::measure_impl(class boost::core::basic_string_view<char>,class boost::core::basic_string_view<char> const *,unsigned __int64 &,class boost::system::error_code &)" (?measure_impl@enc_params_iter_base@detail@urls@boost@@KA_NV?$basic_string_view@D@core@4@PEBV564@AEA_KAEAVerror_code@system@4@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: static bool __cdecl boost::urls::detail::enc_segs_iter_base::measure_impl(class boost::core::basic_string_view<char>,unsigned __int64 &,class boost::system::error_code &)" (?measure_impl@enc_segs_iter_base@detail@urls@boost@@KA_NV?$basic_string_view@D@core@4@AEA_KAEAVerror_code@system@4@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: static void __cdecl boost::urls::detail::plain_params_iter_base::measure_impl(class boost::core::basic_string_view<char>,class boost::core::basic_string_view<char> const *,unsigned __int64 &)" (?measure_impl@plain_params_iter_base@detail@urls@boost@@KAXV?$basic_string_view@D@core@4@PEBV564@AEA_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "protected: static void __cdecl boost::urls::detail::plain_segs_iter_base::measure_impl(class boost::core::basic_string_view<char>,unsigned __int64 &)" (?measure_impl@plain_segs_iter_base@detail@urls@boost@@KAXV?$basic_string_view@D@core@4@AEA_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::normalize(void)" (?normalize@url@urls@boost@@QEAAAEAV123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: struct boost::urls::url::raw_param __cdecl boost::urls::url::param(unsigned __int64)const " (?param@url@urls@boost@@AEBA?AUraw_param@123@_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::absolute_uri_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::absolute_uri_rule &)" (?parse@absolute_uri_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::authority_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::authority_rule &)" (?parse@authority_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::fragment_part_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::fragment_part_rule &)" (?parse@fragment_part_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::fragment_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::fragment_rule &)" (?parse@fragment_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::hier_part_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::hier_part_rule &)" (?parse@hier_part_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::host_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::host_rule &)" (?parse@host_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::ip_literal_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::ip_literal_rule &)" (?parse@ip_literal_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::ipv4_address::parse(char const * &,char const * const,class boost::system::error_code &,class boost::urls::ipv4_address &)" (?parse@ipv4_address@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAV123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::ipv6_address::parse(char const * &,char const * const,class boost::system::error_code &,class boost::urls::ipv6_address &)" (?parse@ipv6_address@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAV123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::ipv_future_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::ipv_future_rule &)" (?parse@ipv_future_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::port_part_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::port_part_rule &)" (?parse@port_part_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::port_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::port_rule &)" (?parse@port_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::query_part_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::query_part_rule &)" (?parse@query_part_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::reg_name_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::reg_name_rule &)" (?parse@reg_name_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::relative_part_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::relative_part_rule &)" (?parse@relative_part_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::relative_ref_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::relative_ref_rule &)" (?parse@relative_ref_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::scheme_part_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::scheme_part_rule &)" (?parse@scheme_part_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::scheme_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::scheme_rule &)" (?parse@scheme_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::segment_nz_nc_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::segment_nz_nc_rule const &)" (?parse@segment_nz_nc_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEBU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::segment_nz_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::segment_nz_rule const &)" (?parse@segment_nz_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEBU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::segment_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::segment_rule const &)" (?parse@segment_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEBU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::uri_reference_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::uri_reference_rule &)" (?parse@uri_reference_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::uri_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::uri_rule &)" (?parse@uri_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: static void __cdecl boost::urls::userinfo_rule::parse(char const * &,char const * const,class boost::system::error_code &,struct boost::urls::userinfo_rule &)" (?parse@userinfo_rule@urls@boost@@CAXAEAPEBDQEBDAEAVerror_code@system@3@AEAU123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class boost::system::result<class boost::urls::url_view,class boost::system::error_code> __cdecl boost::urls::parse_absolute_uri(class boost::core::basic_string_view<char>)" (?parse_absolute_uri@urls@boost@@YA?AV?$result@Vurl_view@urls@boost@@Verror_code@system@3@@system@2@V?$basic_string_view@D@core@2@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class boost::system::result<class boost::urls::authority_view,class boost::system::error_code> __cdecl boost::urls::parse_authority(class boost::core::basic_string_view<char>)" (?parse_authority@urls@boost@@YA?AV?$result@Vauthority_view@urls@boost@@Verror_code@system@3@@system@2@V?$basic_string_view@D@core@2@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class boost::system::result<class boost::urls::ipv4_address,class boost::system::error_code> __cdecl boost::urls::parse_ipv4_address(class boost::core::basic_string_view<char>)" (?parse_ipv4_address@urls@boost@@YA?AV?$result@Vipv4_address@urls@boost@@Verror_code@system@3@@system@2@V?$basic_string_view@D@core@2@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class boost::system::result<class boost::urls::ipv6_address,class boost::system::error_code> __cdecl boost::urls::parse_ipv6_address(class boost::core::basic_string_view<char>)" (?parse_ipv6_address@urls@boost@@YA?AV?$result@Vipv6_address@urls@boost@@Verror_code@system@3@@system@2@V?$basic_string_view@D@core@2@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class boost::system::result<class boost::urls::segments_encoded_view,class boost::system::error_code> __cdecl boost::urls::parse_path(class boost::core::basic_string_view<char>)" (?parse_path@urls@boost@@YA?AV?$result@Vsegments_encoded_view@urls@boost@@Verror_code@system@3@@system@2@V?$basic_string_view@D@core@2@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class boost::system::result<class boost::urls::segments_encoded_view,class boost::system::error_code> __cdecl boost::urls::parse_path_abempty(class boost::core::basic_string_view<char>)" (?parse_path_abempty@urls@boost@@YA?AV?$result@Vsegments_encoded_view@urls@boost@@Verror_code@system@3@@system@2@V?$basic_string_view@D@core@2@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class boost::system::result<class boost::urls::segments_encoded_view,class boost::system::error_code> __cdecl boost::urls::parse_path_absolute(class boost::core::basic_string_view<char>)" (?parse_path_absolute@urls@boost@@YA?AV?$result@Vsegments_encoded_view@urls@boost@@Verror_code@system@3@@system@2@V?$basic_string_view@D@core@2@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class boost::system::result<class boost::urls::segments_encoded_view,class boost::system::error_code> __cdecl boost::urls::parse_path_noscheme(class boost::core::basic_string_view<char>)" (?parse_path_noscheme@urls@boost@@YA?AV?$result@Vsegments_encoded_view@urls@boost@@Verror_code@system@3@@system@2@V?$basic_string_view@D@core@2@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class boost::system::result<class boost::urls::segments_encoded_view,class boost::system::error_code> __cdecl boost::urls::parse_path_rootless(class boost::core::basic_string_view<char>)" (?parse_path_rootless@urls@boost@@YA?AV?$result@Vsegments_encoded_view@urls@boost@@Verror_code@system@3@@system@2@V?$basic_string_view@D@core@2@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class boost::system::result<class boost::urls::params_encoded_view,class boost::system::error_code> __cdecl boost::urls::parse_query_params(class boost::core::basic_string_view<char>)" (?parse_query_params@urls@boost@@YA?AV?$result@Vparams_encoded_view@urls@boost@@Verror_code@system@3@@system@2@V?$basic_string_view@D@core@2@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class boost::system::result<class boost::urls::url_view,class boost::system::error_code> __cdecl boost::urls::parse_relative_ref(class boost::core::basic_string_view<char>)" (?parse_relative_ref@urls@boost@@YA?AV?$result@Vurl_view@urls@boost@@Verror_code@system@3@@system@2@V?$basic_string_view@D@core@2@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class boost::system::result<class boost::urls::url_view,class boost::system::error_code> __cdecl boost::urls::parse_uri(class boost::core::basic_string_view<char>)" (?parse_uri@urls@boost@@YA?AV?$result@Vurl_view@urls@boost@@Verror_code@system@3@@system@2@V?$basic_string_view@D@core@2@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class boost::system::result<class boost::urls::url_view,class boost::system::error_code> __cdecl boost::urls::parse_uri_reference(class boost::core::basic_string_view<char>)" (?parse_uri_reference@urls@boost@@YA?AV?$result@Vurl_view@urls@boost@@Verror_code@system@3@@system@2@V?$basic_string_view@D@core@2@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "unsigned __int64 __cdecl boost::urls::pct_decode_bytes_unchecked(class boost::core::basic_string_view<char>)" (?pct_decode_bytes_unchecked@urls@boost@@YA_KV?$basic_string_view@D@core@2@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "unsigned __int64 __cdecl boost::urls::pct_decode_unchecked(char *,char const *,class boost::core::basic_string_view<char>,struct boost::urls::pct_decode_opts const &)" (?pct_decode_unchecked@urls@boost@@YA_KPEADPEBDV?$basic_string_view@D@core@2@AEBUpct_decode_opts@12@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::authority_view::port(void)const " (?port@authority_view@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::url_view::port(void)const " (?port@url_view@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: unsigned short __cdecl boost::urls::authority_view::port_number(void)const " (?port_number@authority_view@urls@boost@@QEBAGXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: unsigned short __cdecl boost::urls::url_view::port_number(void)const " (?port_number@url_view@urls@boost@@QEBAGXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: unsigned __int64 __cdecl boost::urls::ipv4_address::print_impl(char *)const " (?print_impl@ipv4_address@urls@boost@@AEBA_KPEAD@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: unsigned __int64 __cdecl boost::urls::ipv6_address::print_impl(char *)const " (?print_impl@ipv6_address@urls@boost@@AEBA_KPEAD@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::remove_authority(void)" (?remove_authority@url@urls@boost@@QEAAAEAV123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::remove_fragment(void)" (?remove_fragment@url@urls@boost@@QEAAAEAV123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::remove_origin(void)" (?remove_origin@url@urls@boost@@QEAAAEAV123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::remove_password(void)" (?remove_password@url@urls@boost@@QEAAAEAV123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::remove_port(void)" (?remove_port@url@urls@boost@@QEAAAEAV123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::remove_query(void)" (?remove_query@url@urls@boost@@QEAAAEAV123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::remove_scheme(void)" (?remove_scheme@url@urls@boost@@QEAAAEAV123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::remove_user(void)" (?remove_user@url@urls@boost@@QEAAAEAV123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::remove_userinfo(void)" (?remove_userinfo@url@urls@boost@@QEAAAEAV123@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params::iterator __cdecl boost::urls::params::remove_value(class boost::urls::params::iterator)" (?remove_value@params@urls@boost@@QEAA?AViterator@123@V4123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params_encoded::iterator __cdecl boost::urls::params_encoded::remove_value(class boost::urls::params_encoded::iterator)" (?remove_value@params_encoded@urls@boost@@QEAA?AViterator@123@V4123@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params::iterator __cdecl boost::urls::params::replace_value(class boost::urls::params::iterator,class boost::core::basic_string_view<char>)" (?replace_value@params@urls@boost@@QEAA?AViterator@123@V4123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::params_encoded::iterator __cdecl boost::urls::params_encoded::replace_value(class boost::urls::params_encoded::iterator,class boost::core::basic_string_view<char>)" (?replace_value@params_encoded@urls@boost@@QEAA?AViterator@123@V4123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: void __cdecl boost::urls::url::reserve_bytes(unsigned __int64)" (?reserve_bytes@url@urls@boost@@QEAAX_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: char * __cdecl boost::urls::url::resize_impl(int,int,unsigned __int64)" (?resize_impl@url@urls@boost@@AEAAPEADHH_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: char * __cdecl boost::urls::url::resize_impl(int,unsigned __int64)" (?resize_impl@url@urls@boost@@AEAAPEADH_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: bool __cdecl boost::urls::url::resolve(class boost::urls::url_view const &,class boost::urls::url_view const &,class boost::system::error_code &)" (?resolve@url@urls@boost@@AEAA_NAEBVurl_view@23@0AEAVerror_code@system@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::params_encoded_view::iterator::scan(void)" (?scan@iterator@params_encoded_view@urls@boost@@AEAAXXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::params_view::iterator::scan(void)" (?scan@iterator@params_view@urls@boost@@AEAAXXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::url_view::scheme(void)const " (?scheme@url_view@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: enum boost::urls::scheme __cdecl boost::urls::url_view::scheme_id(void)const " (?scheme_id@url_view@urls@boost@@QEBA?AW4scheme@23@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: unsigned __int64 __cdecl boost::urls::url::segment(unsigned __int64)const " (?segment@url@urls@boost@@AEBA_K_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_encoded_authority(class boost::core::basic_string_view<char>)" (?set_encoded_authority@url@urls@boost@@QEAAAEAV123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_encoded_fragment(class boost::core::basic_string_view<char>)" (?set_encoded_fragment@url@urls@boost@@QEAAAEAV123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_encoded_host(class boost::core::basic_string_view<char>)" (?set_encoded_host@url@urls@boost@@QEAAAEAV123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_encoded_password(class boost::core::basic_string_view<char>)" (?set_encoded_password@url@urls@boost@@QEAAAEAV123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_encoded_path(class boost::core::basic_string_view<char>)" (?set_encoded_path@url@urls@boost@@QEAAAEAV123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_encoded_query(class boost::core::basic_string_view<char>)" (?set_encoded_query@url@urls@boost@@QEAAAEAV123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_encoded_user(class boost::core::basic_string_view<char>)" (?set_encoded_user@url@urls@boost@@QEAAAEAV123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_encoded_userinfo(class boost::core::basic_string_view<char>)" (?set_encoded_userinfo@url@urls@boost@@QEAAAEAV123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_fragment(class boost::core::basic_string_view<char>)" (?set_fragment@url@urls@boost@@QEAAAEAV123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: char * __cdecl boost::urls::url::set_fragment_impl(unsigned __int64)" (?set_fragment_impl@url@urls@boost@@AEAAPEAD_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_host(class boost::urls::ipv4_address const &)" (?set_host@url@urls@boost@@QEAAAEAV123@AEBVipv4_address@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_host(class boost::urls::ipv6_address const &)" (?set_host@url@urls@boost@@QEAAAEAV123@AEBVipv6_address@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_host(class boost::core::basic_string_view<char>)" (?set_host@url@urls@boost@@QEAAAEAV123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: char * __cdecl boost::urls::url::set_host_impl(unsigned __int64)" (?set_host_impl@url@urls@boost@@AEAAPEAD_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_password(class boost::core::basic_string_view<char>)" (?set_password@url@urls@boost@@QEAAAEAV123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: char * __cdecl boost::urls::url::set_password_impl(unsigned __int64)" (?set_password_impl@url@urls@boost@@AEAAPEAD_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_path(class boost::core::basic_string_view<char>)" (?set_path@url@urls@boost@@QEAAAEAV123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: bool __cdecl boost::urls::url::set_path_absolute(bool)" (?set_path_absolute@url@urls@boost@@QEAA_N_N@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_port(unsigned short)" (?set_port@url@urls@boost@@QEAAAEAV123@G@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_port(class boost::core::basic_string_view<char>)" (?set_port@url@urls@boost@@QEAAAEAV123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: char * __cdecl boost::urls::url::set_port_impl(unsigned __int64)" (?set_port_impl@url@urls@boost@@AEAAPEAD_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_query(class boost::core::basic_string_view<char>)" (?set_query@url@urls@boost@@QEAAAEAV123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_scheme(class boost::core::basic_string_view<char>)" (?set_scheme@url@urls@boost@@QEAAAEAV123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_scheme(enum boost::urls::scheme)" (?set_scheme@url@urls@boost@@QEAAAEAV123@W4scheme@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void __cdecl boost::urls::url::set_scheme_impl(class boost::core::basic_string_view<char>,enum boost::urls::scheme)" (?set_scheme_impl@url@urls@boost@@AEAAXV?$basic_string_view@D@core@3@W4scheme@23@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_user(class boost::core::basic_string_view<char>)" (?set_user@url@urls@boost@@QEAAAEAV123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: char * __cdecl boost::urls::url::set_user_impl(unsigned __int64)" (?set_user_impl@url@urls@boost@@AEAAPEAD_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::urls::url & __cdecl boost::urls::url::set_userinfo(class boost::core::basic_string_view<char>)" (?set_userinfo@url@urls@boost@@QEAAAEAV123@V?$basic_string_view@D@core@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: char * __cdecl boost::urls::url::set_userinfo_impl(unsigned __int64)" (?set_userinfo_impl@url@urls@boost@@AEAAPEAD_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "enum boost::urls::scheme __cdecl boost::urls::string_to_scheme(class boost::core::basic_string_view<char>)" (?string_to_scheme@urls@boost@@YA?AW4scheme@12@V?$basic_string_view@D@core@2@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "private: void * * __cdecl boost::urls::basic_static_pool::table(void)" (?table@basic_static_pool@urls@boost@@AEAAPEAPEAXXZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "void __cdecl boost::urls::detail::throw_bad_alloc(struct boost::source_location const &)" (?throw_bad_alloc@detail@urls@boost@@YAXAEBUsource_location@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "void __cdecl boost::urls::detail::throw_invalid_argument(struct boost::source_location const &)" (?throw_invalid_argument@detail@urls@boost@@YAXAEBUsource_location@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "void __cdecl boost::urls::detail::throw_invalid_argument(char const *,struct boost::source_location const &)" (?throw_invalid_argument@detail@urls@boost@@YAXPEBDAEBUsource_location@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "void __cdecl boost::urls::detail::throw_length_error(char const *,struct boost::source_location const &)" (?throw_length_error@detail@urls@boost@@YAXPEBDAEBUsource_location@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "void __cdecl boost::urls::detail::throw_out_of_range(struct boost::source_location const &)" (?throw_out_of_range@detail@urls@boost@@YAXAEBUsource_location@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "void __cdecl boost::urls::detail::throw_system_error(class boost::system::error_code const &,struct boost::source_location const &)" (?throw_system_error@detail@urls@boost@@YAXAEBVerror_code@system@3@AEBUsource_location@3@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::ipv4_address::to_buffer(char *,unsigned __int64)const " (?to_buffer@ipv4_address@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@PEAD_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class boost::core::basic_string_view<char> __cdecl boost::urls::ipv6_address::to_buffer(char *,unsigned __int64)const " (?to_buffer@ipv6_address@urls@boost@@QEBA?AV?$basic_string_view@D@core@3@PEAD_K@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: class std::array<unsigned char,4> __cdecl boost::urls::ipv4_address::to_bytes(void)const " (?to_bytes@ipv4_address@urls@boost@@QEBA?AV?$array@E$03@std@@XZ) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "class boost::core::basic_string_view<char> __cdecl boost::urls::to_string(enum boost::urls::scheme)" (?to_string@urls@boost@@YA?AV?$basic_string_view@D@core@2@W4scheme@12@@Z) 已经在 main.obj 中定义  
1>other_cpp.obj : error LNK2005: "public: unsigned int __cdecl boost::urls::ipv4_address::to_uint(void)const " (?to_uint@ipv4_address@urls@boost@@QEBAIXZ) 已经在 main.obj 中定义  
1>E:\workspace\my\test_boost_url\x64\Debug\test_boost_url.exe : fatal error LNK1169: 找到一个或多个多重定义的符号  
1>已完成生成项目“test_boost_url.vcxproj”的操作 - 失败。  
========== 全部重新生成: 成功 0 个，失败 1 个，跳过 0 个 ==========

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-03-16 13:55:39 UTC  
> Url: https://github.com/boostorg/url/issues/137#issuecomment-1069157965  

@itmanfei, It seems like you are including  
  
```cpp  
#define BOOST_URL_NO_LIB   
#include <boost/url/src.hpp>  
```  
  
in two source files. Each with its main function.   
  
This is [not right](https://develop.url.cpp.al/url/quick_look.html#url.quick_look.headers).   
  
- `<boost/url/src.hpp>` should be included in exactly one of your source files.   
- Then in the other source file, you can include   
  
```cpp  
#define BOOST_URL_NO_LIB  
#include <boost/url.hpp>  
```  
  
How are you linking these source files? If you have a single source file, you can do it all in the same file:  
  
```cpp  
#define BOOST_URL_NO_LIB  
#include <boost/url/src.hpp>  
#include <boost/url.hpp>  
```  
  
Could you try that first? It's also becoming difficult to read the comments in this issue. If it doesn't work, could you (i) temporarily set your compiler output to English, and (ii) move your console out to a [gist](https://gist.github.com/) so you can just share the links?
