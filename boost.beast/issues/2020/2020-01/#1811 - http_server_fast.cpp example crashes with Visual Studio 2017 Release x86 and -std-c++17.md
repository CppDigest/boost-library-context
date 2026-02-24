# #1811 - http_server_fast.cpp example crashes with Visual Studio 2017 Release x86 and /std:c++17 [Closed]

> Username: vesterij  
> Created at: 2020-01-20 20:40:09 UTC  
> Updated at: 2022-09-24 05:43:07 UTC  
> Closed at: 2022-09-24 05:43:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1811  

This might not be related to beast but I noticed problem when I changed my beast project to use **/std:c++17**  
  
### Environment  
Boost version 1.72.0  
OS: Windows 10 (10.0.18363 Build 18363)  
  
BOOST_BEAST_VERSION 277  
Windows SDK Version: 10.0.17763.0   
Platform toolset: Visual Studio 2017 (v141)  
  
### How to reproduce  
  
- Create new project with VS2017 -> Visual C++ -> Console App  
- Copy http_server_fast.cpp and fields_alloc.hpp  
- Add boost to additional include directories and additional library directories  
- Change C/C++ -> Language -> C++ Language standard to (/std:c++17)  
- Add Preprocessor definition /D "_SILENCE_CXX17_ALLOCATOR_VOID_DEPRECATION_WARNING"   
- Compile Win32/x86 Release version  
- Any query (successful or not) causes exception and crash  
  
### What makes this interesting/weird  
  
- Example works with x86 Debug version  
- Example works with x64 Debug version  
- Example works with x64 Release version  
- Example works in Linux with g++ and clang++ (of course)  
- Example works with x86 Release version if /std:c++17 is removed  
- Example works with x86 Release version if -O2 changed to -O1 or removed  
- Example works with x86 Release version if NDEBUG is removed (wait? what?)  
  
For now my workaround is to remove NDEBUG and I do not need x86 release build. It just bothers me that it seemingly randomly doesn't work.  
  
Is there something obvious that I am missing here?  
  
Typical stacktrace:  
`  
00feee90()	Unknown  
[Frames below may be incorrect and/or missing]	Unknown  
[External Code]	  
[Inline Frame] fastbeast.exe!boost::asio::executor::on_work_started() Line 137	C++  
fastbeast.exe!boost::asio::executor_work_guard<boost::asio::executor>::executor_work_guard<boost::asio::executor>(const boost::asio::executor & e) Line 46	C++  
[Inline Frame] fastbeast.exe!boost::asio::executor::on_work_started() Line 137	C++  
fastbeast.exe!boost::asio::executor_work_guard<boost::asio::executor>::executor_work_guard<boost::asio::executor>(const boost::asio::executor & e) Line 46	C++  
fastbeast.exe!boost::beast::http::basic_parser<1>::parse_start_line(const char * & in, const char * last, boost::system::error_code & ec, std::integral_constant<bool,1> __formal) Line 334	C++  
[Inline Frame] fastbeast.exe!boost::beast::http::basic_parser<1>::finish_header(boost::system::error_code &) Line 485	C++  
fastbeast.exe!boost::beast::http::basic_parser<1>::put(boost::asio::const_buffer buffer, boost::system::error_code & ec) Line 214	C++  
[Inline Frame] fastbeast.exe!boost::asio::detail::executor_function_base::complete() Line 32	C++  
[Inline Frame] fastbeast.exe!boost::asio::executor::function::operator()() Line 69	C++  
fastbeast.exe!boost::asio::asio_handler_invoke<boost::asio::executor::function>(boost::asio::executor::function & function, ...) Line 69	C++  
[Inline Frame] fastbeast.exe!boost_asio_handler_invoke_helpers::invoke(boost::asio::executor::function &) Line 37	C++  
fastbeast.exe!boost::asio::io_context::executor_type::dispatch<boost::asio::executor::function,std::allocator<void> >(boost::asio::executor::function && f, const std::allocator<void> & a) Line 265	C++  
fastbeast.exe!boost::asio::executor::impl<boost::asio::io_context::executor_type,std::allocator<void> >::dispatch(boost::asio::executor::function && f) Line 185	C++  
fastbeast.exe!boost::asio::executor::dispatch<boost::asio::detail::binder2<boost::beast::detail::dynamic_read_ops::read_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp,boost::asio::executor>,boost::beast::flat_static_buffer<8192>,boost::beast::http::detail::read_all_condition<1>,<lambda_9ef0a7287907fd4600776d7e86a31276> >,boost::system::error_code,unsigned int>,std::allocator<void> >(boost::asio::detail::binder2<boost::beast::detail::dynamic_read_ops::read_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp,boost::asio::executor>,boost::beast::flat_static_buffer<8192>,boost::beast::http::detail::read_all_condition<1>,<lambda_9ef0a7287907fd4600776d7e86a31276> >,boost::system::error_code,unsigned int> && f, const std::allocator<void> & a) Line 351	C++  
[Inline Frame] fastbeast.exe!boost::asio::detail::handler_work<boost::beast::detail::dynamic_read_ops::read_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp,boost::asio::executor>,boost::beast::flat_static_buffer<8192>,boost::beast::http::detail::read_all_condition<1>,<lambda_9ef0a7287907fd4600776d7e86a31276> >,boost::asio::detail::io_object_executor<boost::asio::executor>,boost::asio::executor>::complete(boost::asio::detail::binder2<boost::beast::detail::dynamic_read_ops::read_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp,boost::asio::executor>,boost::beast::flat_static_buffer<8192>,boost::beast::http::detail::read_all_condition<1>,<lambda_9ef0a7287907fd4600776d7e86a31276> >,boost::system::error_code,unsigned int> &) Line 72	C++  
fastbeast.exe!boost::asio::detail::win_iocp_socket_recv_op<boost::asio::mutable_buffer,boost::beast::detail::dynamic_read_ops::read_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp,boost::asio::executor>,boost::beast::flat_static_buffer<8192>,boost::beast::http::detail::read_all_condition<1>,<lambda_9ef0a7287907fd4600776d7e86a31276> >,boost::asio::detail::io_object_executor<boost::asio::executor> >::do_complete(void * owner, boost::asio::detail::win_iocp_operation * base, const boost::system::error_code & result_ec, unsigned int bytes_transferred) Line 103	C++  
[Inline Frame] fastbeast.exe!boost::asio::detail::win_iocp_operation::complete(void *) Line 47	C++  
fastbeast.exe!boost::asio::detail::win_iocp_io_context::do_one(unsigned long ec, boost::system::error_code &) Line 460	C++  
fastbeast.exe!boost::asio::detail::win_iocp_io_context::run(boost::system::error_code & ec) Line 202	C++  
[Inline Frame] fastbeast.exe!boost::asio::io_context::run() Line 63	C++  
fastbeast.exe!main(int argc, char * * argv) Line 341	C++  
`

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-01-21 01:00:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1811#issuecomment-576474843  

Thanks @vesterij for the fantastically complete and detailed bug report. I'll reproduce it and investigate as soon as I have a moment.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2020-02-20 02:03:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1811#issuecomment-588571409  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-04-03 15:42:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1811#issuecomment-608513837  

@vesterij Well, the good news is that I've reproduced the bug.  
The bad news is that I think you've already found all the workarounds.  
I have filed a bug report with microsoft and will keep you posted if there is any update.

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-04-07 11:28:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1811#issuecomment-610332768  

@vesterij You can follow the conversation with Microsoft here if you're interested.  
  
I have a feeling that we're going to have to add a note to the documentation to avoid c++17 on VS2017.  
  
https://developercommunity.visualstudio.com/content/problem/977251/visual-c-2017-code-generation-error-only-32-bit-wi.html

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:43:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1811#issuecomment-1256872033  

Give the time frame, I assume this is solved.
