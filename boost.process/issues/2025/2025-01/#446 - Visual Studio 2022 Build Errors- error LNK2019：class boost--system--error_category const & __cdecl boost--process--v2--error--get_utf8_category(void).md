# #446 - Visual Studio 2022 Build Errors:  error LNK2019：class boost::system::error_category const & __cdecl boost::process::v2::error::get_utf8_category(void) [Closed]

> Username: ml232528  
> Created at: 2025-01-04 08:46:41 UTC  
> Updated at: 2025-01-13 01:32:29 UTC  
> Closed at: 2025-01-13 01:32:29 UTC  
> Url: https://github.com/boostorg/process/issues/446  

boost 1.87.0  
Visual Studio Community 2022 17.12.3  
  
  
```  
#include <boost/process.hpp>  
#include <boost/process/v2.hpp>  
  
int main()  
{  
	boost::asio::io_context ctx;  
	boost::process::v2::process c{ ctx,"test.exe",{} };  
}  
```  
Build Errors:  
```  
16:45:28:630	生成开始于 16:45...  
16:45:28:810	1>------ 已启动生成: 项目: ConsoleApplication27, 配置: Debug x64 ------  
16:45:28:973	1>ConsoleApplication27.cpp  
16:45:29:803	1>Please define _WIN32_WINNT or _WIN32_WINDOWS appropriately. For example:  
16:45:29:803	1>- add -D_WIN32_WINNT=0x0601 to the compiler command line; or  
16:45:29:803	1>- add _WIN32_WINNT=0x0601 to your project's Preprocessor Definitions.  
16:45:29:803	1>Assuming _WIN32_WINNT=0x0601 (i.e. Windows 7 target).  
16:45:32:664	1>ConsoleApplication27.obj : error LNK2019: 无法解析的外部符号 "class boost::system::error_category const & __cdecl boost::process::v2::error::get_utf8_category(void)" (?get_utf8_category@error@v2@process@boost@@YAAEBVerror_category@system@4@XZ)，函数 "void __cdecl boost::process::v2::error::`dynamic initializer for 'utf8_category''(void)" (??__Eutf8_category@error@v2@process@boost@@YAXXZ) 中引用了该符号  
16:45:32:719	1>ConsoleApplication27.obj : error LNK2019: 无法解析的外部符号 "class boost::system::error_category const & __cdecl boost::process::v2::error::get_exit_code_category(void)" (?get_exit_code_category@error@v2@process@boost@@YAAEBVerror_category@system@4@XZ)，函数 "void __cdecl boost::process::v2::error::`dynamic initializer for 'exit_code_category''(void)" (??__Eexit_code_category@error@v2@process@boost@@YAXXZ) 中引用了该符号  
16:45:32:719	1>ConsoleApplication27.obj : error LNK2019: 无法解析的外部符号 "void __cdecl boost::process::v2::detail::do_throw_error(class boost::system::error_code const &,char const *)" (?do_throw_error@detail@v2@process@boost@@YAXAEBVerror_code@system@4@PEBD@Z)，函数 "void __cdecl boost::process::v2::detail::throw_error(class boost::system::error_code const &,char const *)" (?throw_error@detail@v2@process@boost@@YAXAEBVerror_code@system@4@PEBD@Z) 中引用了该符号  
16:45:32:719	1>ConsoleApplication27.obj : error LNK2019: 无法解析的外部符号 "unsigned __int64 __cdecl boost::process::v2::detail::size_as_wide(char const *,unsigned __int64,class boost::system::error_code &)" (?size_as_wide@detail@v2@process@boost@@YA_KPEBD_KAEAVerror_code@system@4@@Z)，函数 "class std::basic_string<wchar_t,struct std::char_traits<wchar_t>,class std::allocator<wchar_t> > __cdecl boost::process::v2::detail::conv_string<wchar_t,struct std::char_traits<wchar_t>,class std::allocator<wchar_t>,void>(char const *,unsigned __int64,class std::allocator<wchar_t>)" (??$conv_string@_WU?$char_traits@_W@std@@V?$allocator@_W@2@X@detail@v2@process@boost@@YA?AV?$basic_string@_WU?$char_traits@_W@std@@V?$allocator@_W@2@@std@@PEBD_KV?$allocator@_W@5@@Z) 中引用了该符号  
16:45:32:719	1>ConsoleApplication27.obj : error LNK2019: 无法解析的外部符号 "unsigned __int64 __cdecl boost::process::v2::detail::convert_to_wide(char const *,unsigned __int64,wchar_t *,unsigned __int64,class boost::system::error_code &)" (?convert_to_wide@detail@v2@process@boost@@YA_KPEBD_KPEA_W1AEAVerror_code@system@4@@Z)，函数 "class std::basic_string<wchar_t,struct std::char_traits<wchar_t>,class std::allocator<wchar_t> > __cdecl boost::process::v2::detail::conv_string<wchar_t,struct std::char_traits<wchar_t>,class std::allocator<wchar_t>,void>(char const *,unsigned __int64,class std::allocator<wchar_t>)" (??$conv_string@_WU?$char_traits@_W@std@@V?$allocator@_W@2@X@detail@v2@process@boost@@YA?AV?$basic_string@_WU?$char_traits@_W@std@@V?$allocator@_W@2@@std@@PEBD_KV?$allocator@_W@5@@Z) 中引用了该符号  
16:45:32:719	1>ConsoleApplication27.obj : error LNK2019: 无法解析的外部符号 "class boost::system::error_code __cdecl boost::process::v2::detail::get_last_error(void)" (?get_last_error@detail@v2@process@boost@@YA?AVerror_code@system@4@XZ)，函数 "public: struct boost::process::v2::basic_process<class boost::asio::any_io_executor> __cdecl boost::process::v2::windows::default_launcher::operator()<class boost::asio::any_io_executor,class std::initializer_list<class boost::basic_string_view<char,struct std::char_traits<char> > > &>(class boost::asio::any_io_executor,class boost::system::error_code &,class boost::filesystem::path const &,class std::initializer_list<class boost::basic_string_view<char,struct std::char_traits<char> > > &)" (??$?RVany_io_executor@asio@boost@@AEAV?$initializer_list@V?$basic_string_view@DU?$char_traits@D@std@@@boost@@@std@@$$V@default_launcher@windows@v2@process@boost@@QEAA?AU?$basic_process@Vany_io_executor@asio@boost@@@234@Vany_io_executor@asio@4@AEAVerror_code@system@4@AEBVpath@filesystem@4@AEAV?$initializer_list@V?$basic_string_view@DU?$char_traits@D@std@@@boost@@@std@@@Z) 中引用了该符号  
16:45:32:719	1>ConsoleApplication27.obj : error LNK2019: 无法解析的外部符号 "public: static unsigned __int64 __cdecl boost::process::v2::windows::default_launcher::escaped_argv_length(class boost::basic_string_view<wchar_t,struct std::char_traits<wchar_t> >)" (?escaped_argv_length@default_launcher@windows@v2@process@boost@@SA_KV?$basic_string_view@_WU?$char_traits@_W@std@@@5@@Z)，函数 "public: static class std::basic_string<wchar_t,struct std::char_traits<wchar_t>,class std::allocator<wchar_t> > __cdecl boost::process::v2::windows::default_launcher::build_command_line_impl<class std::vector<class std::basic_string<wchar_t,struct std::char_traits<wchar_t>,class std::allocator<wchar_t> >,class std::allocator<class std::basic_string<wchar_t,struct std::char_traits<wchar_t>,class std::allocator<wchar_t> > > > >(class boost::filesystem::path const &,class std::vector<class std::basic_string<wchar_t,struct std::char_traits<wchar_t>,class std::allocator<wchar_t> >,class std::allocator<class std::basic_string<wchar_t,struct std::char_traits<wchar_t>,class std::allocator<wchar_t> > > > const &,class boost::basic_string_view<wchar_t,struct std::char_traits<wchar_t> >)" (??$build_command_line_impl@V?$vector@V?$basic_string@_WU?$char_traits@_W@std@@V?$allocator@_W@2@@std@@V?$allocator@V?$basic_string@_WU?$char_traits@_W@std@@V?$allocator@_W@2@@std@@@2@@std@@@default_launcher@windows@v2@process@boost@@SA?AV?$basic_string@_WU?$char_traits@_W@std@@V?$allocator@_W@2@@std@@AEBVpath@filesystem@4@AEBV?$vector@V?$basic_string@_WU?$char_traits@_W@std@@V?$allocator@_W@2@@std@@V?$allocator@V?$basic_string@_WU?$char_traits@_W@std@@V?$allocator@_W@2@@std@@@2@@6@V?$basic_string_view@_WU?$char_traits@_W@std@@@4@@Z) 中引用了该符号  
16:45:32:719	1>ConsoleApplication27.obj : error LNK2019: 无法解析的外部符号 "public: static unsigned __int64 __cdecl boost::process::v2::windows::default_launcher::escape_argv_string(wchar_t *,unsigned __int64,class boost::basic_string_view<wchar_t,struct std::char_traits<wchar_t> >)" (?escape_argv_string@default_launcher@windows@v2@process@boost@@SA_KPEA_W_KV?$basic_string_view@_WU?$char_traits@_W@std@@@5@@Z)，函数 "public: static class std::basic_string<wchar_t,struct std::char_traits<wchar_t>,class std::allocator<wchar_t> > __cdecl boost::process::v2::windows::default_launcher::build_command_line_impl<class std::vector<class std::basic_string<wchar_t,struct std::char_traits<wchar_t>,class std::allocator<wchar_t> >,class std::allocator<class std::basic_string<wchar_t,struct std::char_traits<wchar_t>,class std::allocator<wchar_t> > > > >(class boost::filesystem::path const &,class std::vector<class std::basic_string<wchar_t,struct std::char_traits<wchar_t>,class std::allocator<wchar_t> >,class std::allocator<class std::basic_string<wchar_t,struct std::char_traits<wchar_t>,class std::allocator<wchar_t> > > > const &,class boost::basic_string_view<wchar_t,struct std::char_traits<wchar_t> >)" (??$build_command_line_impl@V?$vector@V?$basic_string@_WU?$char_traits@_W@std@@V?$allocator@_W@2@@std@@V?$allocator@V?$basic_string@_WU?$char_traits@_W@std@@V?$allocator@_W@2@@std@@@2@@std@@@default_launcher@windows@v2@process@boost@@SA?AV?$basic_string@_WU?$char_traits@_W@std@@V?$allocator@_W@2@@std@@AEBVpath@filesystem@4@AEBV?$vector@V?$basic_string@_WU?$char_traits@_W@std@@V?$allocator@_W@2@@std@@V?$allocator@V?$basic_string@_WU?$char_traits@_W@std@@V?$allocator@_W@2@@std@@@2@@6@V?$basic_string_view@_WU?$char_traits@_W@std@@@4@@Z) 中引用了该符号  
16:45:32:719	1>ConsoleApplication27.obj : error LNK2019: 无法解析的外部符号 "void __cdecl boost::process::v2::detail::terminate_if_running_(void *)" (?terminate_if_running_@detail@v2@process@boost@@YAXPEAX@Z)，函数 "public: void __cdecl boost::process::v2::detail::basic_process_handle_win<class boost::asio::any_io_executor>::terminate_if_running(void)" (?terminate_if_running@?$basic_process_handle_win@Vany_io_executor@asio@boost@@@detail@v2@process@boost@@QEAAXXZ) 中引用了该符号  
16:45:32:719	1>C:\Users\kk\source\repos\ConsoleApplication27\x64\Debug\ConsoleApplication27.exe : fatal error LNK1120: 9 个无法解析的外部命令  
16:45:32:743	1>已完成生成项目“ConsoleApplication27.vcxproj”的操作 - 失败。  
16:45:32:759	========== 生成: 0 成功，1 失败，0 最新，0 已跳过 ==========  
16:45:32:759	========== 生成 于 16:45 完成，耗时 04.189 秒 ==========  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-01-06 06:19:38 UTC  
> Url: https://github.com/boostorg/process/issues/446#issuecomment-2572375989  

Are you linking to  boost_process?

---

## Comment 2

> Username: ml232528  
> Created at: 2025-01-08 07:01:31 UTC  
> Url: https://github.com/boostorg/process/issues/446#issuecomment-2576888113  

linking to boost_process?  
#pragma comment(lib,"libboost_process-vc143-mt-gd-x64-1_87.lib") ？  
Boost should auto link.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2025-01-08 10:11:04 UTC  
> Url: https://github.com/boostorg/process/issues/446#issuecomment-2577292553  

I added auto-linking in 64fc05c, which will be in the next release.
