# #55 - [Feature Reques] Fine-grained control over printing [Closed]

> Username: diehard2  
> Created at: 2023-11-22 21:45:56 UTC  
> Updated at: 2024-10-23 06:52:27 UTC  
> Closed at: 2024-10-23 06:52:27 UTC  
> Url: https://github.com/boostorg/exception/issues/55  

I'm allowing people to add arbitrary tags, however diagnostic_information() is causing some formatting I don't love. However I don't see any good way to create my own version of that function and get the formatting I would like. The closest I've come to seeing a solution is here  
  
https://stackoverflow.com/questions/48191012/how-to-iterate-over-all-error-infos-in-boostexception  
  
Thanks for any help or workarounds and apologies if this isn't where I should submit something like this

---

## Comment 1

> Username: zajo  
> Created at: 2023-11-23 02:35:11 UTC  
> Url: https://github.com/boostorg/exception/issues/55#issuecomment-1823754253  

The output from diagnostic_information is not designed to be user-friendly. The intended use of the "arbitrary tags" with Boost Exception is that you'd catch and handle every possible exception type, and in each catch block access the additional info to format a user-friendly message (which in general includes localization); then follow with catch(...) that outputs the diagnostic information, which you would treat as a bug (you're missing a necessary catch statement for some exception type that formats a user-friendly message).  
  
That said, you do have some control over the automatic conversion to string, by providing to_string overloads for error_info<Tag, t>. See https://www.boost.org/doc/libs/1_83_0/libs/exception/doc/diagnostic_information.html.

---

## Comment 2

> Username: diehard2  
> Created at: 2023-11-23 17:26:08 UTC  
> Url: https://github.com/boostorg/exception/issues/55#issuecomment-1824757076  

Thanks @zajo, that gets me closer.  
  
I don't believe I'm using boost::exception in an unintended manner. I have a main program/service with libraries that call libraries, and with C++17 on, we have the ability to nest exceptions. I don't necessarily know what's in those library thrown boost exceptions, so my only recourse for printing it out for logs is to use diagnostic_information(). If I could iterate over the set of error_infos, I could handle the ones I need to with a cast to the types I know about and otherwise print out unknown ones with the generic to_string(). I will say that with the exception of getting the information out of it, this is all working amazingly well. And I can live with the diagnostic_output to my logs, but I was hoping for a more flexible option.  
  
If you can think of any way for me to get this information out, I would really appreciate it. If not, I hope you'll consider this for a feature request. Thanks again!

---

## Comment 3

> Username: zajo  
> Created at: 2023-11-24 05:26:26 UTC  
> Url: https://github.com/boostorg/exception/issues/55#issuecomment-1825165240  

Do I understand correctly that you want the diagnostic info to only include error_infos you don't know about? Otherwise I don't see a point in iterating over them, you don't need iteration in order to handle the error_infos you need.  
  
Do you mind me asking if this is some legacy project? If not, I'd recommend Boost LEAF over Boost Exception.

---

## Comment 4

> Username: zlojvavan  
> Created at: 2023-11-24 09:09:03 UTC  
> Url: https://github.com/boostorg/exception/issues/55#issuecomment-1825354387  

@diehard2 apparently as @zajo explained this great lib wasn't designed with the idea of further extensibility and customization in mind so I also have had to find my ways to get to its internals and also implement few derived/custom classes (error_info, error_info_container etc.) to achieve what I wanted, including localizable and custom per instance error info descriptions etc.  
  
<details><summary>so for example</summary>  
<p>  
	this code  
  
  
```  
	eh::AddErrorInfoDescription<eh::throw_chain>(eh::language_id_t{ "en" }, ">>>>> throw exception chain <<<<<");  
	eh::AddErrorInfoDescription<eh::errinfo_file_name>("<file name>");  
	try  
	{  
		try  
		{  
			auto const address = net::ip::make_address("localhost");  
		}  
		catch (std::exception const& e)  
		{  
			boost::variant2::variant<int, char const*> someVar{ "1352345" };  
			RETHROW(/*BOOSTED_EXCEPTION*/(e)  
				<< MAKE_ERROR_INFO("rethrowing here", "some description")  
				<< MAKE_ERROR_INFO((boost::variant2::variant<int, char const*>{"1352345"}), "variant"s)  
				<< MAKE_ERROR_INFO(someVar)  
				<< MAKE_ERROR_INFO(someVar,"custom var description")  
				<< eh::errinfo_file_name(__FILE__)  
				<< eh::errinfo_file_name(__FILE__,"this file")  
				<< eh::errinfo_api_function("net::ip::make_address")  
				<< MAKE_ERROR_INFO(std::make_tuple(__FILE__, __LINE__, "test", 5), "custom tuple description"sv)  
				<< MAKE_ERROR_INFO(std::make_tuple(__FILE__, __LINE__, "test", 5))  
				<< MAKE_ERROR_INFO(std::make_tuple(__FILE__, __LINE__, "test", 5),"another tuple")  
				<< eh::errinfo_at_line(__LINE__)  
				<< eh::errinfo_file_name(__FILE__, "duplicate eh::errinfo_file_name")  
			);  
		}  
	}   
	catch (...)  
	{  
		HANDLE_EXCEPTION();  
	}  
  
```  
  
will produce following report:  
  
```  
2023-11-24 08:47:50.8047075: #1 (1)  
  
C:\Project\Libs\vcpkg\installed\x64-windows-static\include\boost\asio\ip\impl\address.ipp(112): Throw in function make_address  
Dynamic exception type: class vavan::eh::boosted_exception<class boost::system::system_error>  
std::exception::what: An invalid argument was supplied [system:10022]  
"system" error #10022  
[errno] = int(22)  
[some description] = const char *(rethrowing here)  
[variant] = class boost::variant2::variant<int,char const *>(1352345)  
[someVar] = class boost::variant2::variant<int,char const *>(1352345)  
[custom var description] = class boost::variant2::variant<int,char const *>(1352345)  
[<file name>] = class vavan::filesystem::path(C:\Project\Test\helperCommon\test_all\test_all.cpp)  
[this file] = class vavan::filesystem::path(C:\Project\Test\helperCommon\test_all\test_all.cpp)  
[API function name] = const char*(net::ip::make_address)  
[custom tuple description] = class std::tuple<char const *,long,char const *,int>(C:\Project\Test\helperCommon\test_all\test_all.cpp, 73, test, 5)  
[std::make_tuple("C:\\Project\\Test\\helperCommon\\test_all\\test_all.cpp", (__LINE__Var+28), "test", 5)] = class std::tuple<char const *,long,char const *,int>(C:\Project\Test\helperCommon\test_all\test_all.cpp, 73, test, 5)  
[another tuple] = class std::tuple<char const *,long,char const *,int>(C:\Project\Test\helperCommon\test_all\test_all.cpp, 73, test, 5)  
[at line #] = int(73)  
[duplicate eh::errinfo_file_name] = class vavan::filesystem::path(C:\Project\Test\helperCommon\test_all\test_all.cpp)  
  
[init exception callstack] =  0# vavan::eh::boost_exception at C:\Project\Test\helperCommon\test_all\helperCommonNew.h:14028  
 1# vavan::eh::make_boosted_exception<boost::system::system_error> at C:\Project\Test\helperCommon\test_all\helperCommonNew.h:14212  
 2# vavan::eh::ThrowExceptionImpl<boost::system::system_error const &> at C:\Project\Test\helperCommon\test_all\helperCommonNew.h:15124  
 3# vavan::eh::ThrowExceptionImpl<boost::system::system_error> at C:\Project\Test\helperCommon\test_all\helperCommonNew.h:15143  
 4# boost::throw_exception<boost::system::system_error &> at C:\Project\Test\helperCommon\test_all\helperCommonNew.h:21268  
 5# boost::asio::detail::do_throw_error at C:\Project\Libs\vcpkg\installed\x64-windows-static\include\boost\asio\detail\impl\throw_error.ipp:33  
 6# boost::asio::detail::throw_error at C:\Project\Libs\vcpkg\installed\x64-windows-static\include\boost\asio\detail\throw_error.hpp:43  
 7# boost::asio::ip::make_address at C:\Project\Libs\vcpkg\installed\x64-windows-static\include\boost\asio\ip\impl\address.ipp:113  
 8# test_rethrow at C:\Project\Test\helperCommon\test_all\test_all.cpp:56  
 9# main at C:\Project\Test\helperCommon\test_all\test_all.cpp:133  
10# invoke_main at D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:79  
11# __scrt_common_main_seh at D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:288  
12# __scrt_common_main at D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:331  
13# mainCRTStartup at D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_main.cpp:17  
14# BaseThreadInitThunk in KERNEL32  
15# RtlUserThreadStart in ntdll  
  
[>>>>> throw exception chain <<<<<] =   
0: 2023-11-24 08:47:50.8046388 thread #23360( "main" )  
C:\Project\Test\helperCommon\test_all\test_all.cpp(73) : Throw in function void __cdecl test_rethrow(void)  
 0# vavan::eh::detail::add_throw_level at C:\Project\Test\helperCommon\test_all\helperCommonNew.h:13946  
 1# vavan::eh::boosted_exception<std::exception>::rethrow at C:\Project\Test\helperCommon\test_all\helperCommonNew.h:14163  
 2# vavan::eh::RethrowImpl<std::exception> at C:\Project\Test\helperCommon\test_all\helperCommonNew.h:15232  
 3# `test_rethrow'::`1'::catch$16 at C:\Project\Test\helperCommon\test_all\test_all.cpp:61  
 4# _CallSettingFrame_LookupContinuationIndex at D:\a\_work\1\s\src\vctools\crt\vcruntime\src\eh\amd64\handlers.asm:98  
 5# __FrameHandler4::CxxCallCatchBlock at D:\a\_work\1\s\src\vctools\crt\vcruntime\src\eh\frame.cpp:1439  
 6# RtlCaptureContext2 in ntdll  
 7# test_rethrow at C:\Project\Test\helperCommon\test_all\test_all.cpp:56  
 8# main at C:\Project\Test\helperCommon\test_all\test_all.cpp:133  
 9# invoke_main at D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:79  
10# __scrt_common_main_seh at D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:288  
11# __scrt_common_main at D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:331  
12# mainCRTStartup at D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_main.cpp:17  
13# BaseThreadInitThunk in KERNEL32  
14# RtlUserThreadStart in ntdll  
  
1: 2023-11-24 08:47:50.8044530 thread #23360( "main" )  
C:\Project\Libs\vcpkg\installed\x64-windows-static\include\boost\asio\ip\impl\address.ipp(112) : Throw in function make_address  
 0# vavan::eh::detail::add_throw_level at C:\Project\Test\helperCommon\test_all\helperCommonNew.h:13946  
 1# vavan::eh::ThrowExceptionImpl<vavan::eh::boosted_exception<boost::system::system_error> > at C:\Project\Test\helperCommon\test_all\helperCommonNew.h:15116  
 2# vavan::eh::ThrowExceptionImpl<boost::system::system_error const &> at C:\Project\Test\helperCommon\test_all\helperCommonNew.h:15126  
 3# vavan::eh::ThrowExceptionImpl<boost::system::system_error> at C:\Project\Test\helperCommon\test_all\helperCommonNew.h:15143  
 4# boost::throw_exception<boost::system::system_error &> at C:\Project\Test\helperCommon\test_all\helperCommonNew.h:21268  
 5# boost::asio::detail::do_throw_error at C:\Project\Libs\vcpkg\installed\x64-windows-static\include\boost\asio\detail\impl\throw_error.ipp:33  
 6# boost::asio::detail::throw_error at C:\Project\Libs\vcpkg\installed\x64-windows-static\include\boost\asio\detail\throw_error.hpp:43  
 7# boost::asio::ip::make_address at C:\Project\Libs\vcpkg\installed\x64-windows-static\include\boost\asio\ip\impl\address.ipp:113  
 8# test_rethrow at C:\Project\Test\helperCommon\test_all\test_all.cpp:56  
 9# main at C:\Project\Test\helperCommon\test_all\test_all.cpp:133  
10# invoke_main at D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:79  
11# __scrt_common_main_seh at D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:288  
12# __scrt_common_main at D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:331  
13# mainCRTStartup at D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_main.cpp:17  
14# BaseThreadInitThunk in KERNEL32  
15# RtlUserThreadStart in ntdll  
  
caught in thread #23360( "main" ) at  
 0# vavan::eh::HandleExceptionImplExceptionCommon<std::exception> at C:\Project\Test\helperCommon\test_all\helperCommonNew.h:14822  
 1# vavan::eh::HandleExceptionImplException<std::exception,char const * &> at C:\Project\Test\helperCommon\test_all\helperCommonNew.h:14961  
 2# vavan::eh::HandleExceptionImplException<std::exception,char const * &> at C:\Project\Test\helperCommon\test_all\helperCommonNew.h:14968  
 3# `vavan::eh::HandleExceptionImpl<char const *>'::`1'::catch$2 at C:\Project\Test\helperCommon\test_all\helperCommonNew.h:15031  
 4# _CallSettingFrame_LookupContinuationIndex at D:\a\_work\1\s\src\vctools\crt\vcruntime\src\eh\amd64\handlers.asm:98  
 5# __FrameHandler4::CxxCallCatchBlock at D:\a\_work\1\s\src\vctools\crt\vcruntime\src\eh\frame.cpp:1439  
 6# RtlCaptureContext2 in ntdll  
 7# vavan::eh::HandleExceptionImpl<char const *> at C:\Project\Test\helperCommon\test_all\helperCommonNew.h:15027  
 8# `test_rethrow'::`1'::catch$17 at C:\Project\Test\helperCommon\test_all\test_all.cpp:82  
 9# _CallSettingFrame_LookupContinuationIndex at D:\a\_work\1\s\src\vctools\crt\vcruntime\src\eh\amd64\handlers.asm:98  
10# __FrameHandler4::CxxCallCatchBlock at D:\a\_work\1\s\src\vctools\crt\vcruntime\src\eh\frame.cpp:1439  
11# RtlCaptureContext2 in ntdll  
12# test_rethrow at C:\Project\Test\helperCommon\test_all\test_all.cpp:56  
13# main at C:\Project\Test\helperCommon\test_all\test_all.cpp:133  
14# invoke_main at D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:79  
15# __scrt_common_main_seh at D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:288  
16# __scrt_common_main at D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_common.inl:331  
17# mainCRTStartup at D:\a\_work\1\s\src\vctools\crt\vcstartup\src\startup\exe_main.cpp:17  
18# BaseThreadInitThunk in KERNEL32  
19# RtlUserThreadStart in ntdll  
  
```  
  
</p>  
</details>

---

## Comment 5

> Username: zajo  
> Created at: 2023-11-24 21:43:02 UTC  
> Updated at: 2023-11-26 04:03:00 UTC  
> Url: https://github.com/boostorg/exception/issues/55#issuecomment-1826099971  

@zlojvavan if you make a pull request with your changes I'll take a look.

---

## Comment 6

> Username: diehard2  
> Created at: 2023-11-26 17:01:50 UTC  
> Url: https://github.com/boostorg/exception/issues/55#issuecomment-1826835825  

@zlojvavan great minds think alike, that's at least partially what I'm doing. with only about 50 lines of code plus boost::exception plus boost stacktrace you can essentially get Java and C# style exceptions.  
  
``` Do I understand correctly that you want the diagnostic info to only include error_infos you don't know about? Otherwise I don't see a point in iterating over them, you don't need iteration in order to handle the error_infos you need.  
  
Do you mind me asking if this is some legacy project? If not, I'd recommend Boost LEAF over Boost Exception.  
```  
  
@zajo my place largely uses exceptions and its unlikely we would switch to a return code paradigm at this late stage. There's just too many millions of lines. I think what would be ideal is if I could remove error_info's. Instead of get_error_info that returns a raw pointer, maybe a unique_ptr? That way I could handle the ones I know about and let diagnostic_information print out the ones I don't. But if I could just iterate over them like diagnostic_information does that would make my life much easier. For people who use exceptions, this library is really great. Thanks for all your hard work.

---

## Comment 7

> Username: zajo  
> Created at: 2023-11-27 00:15:52 UTC  
> Updated at: 2023-11-27 00:20:05 UTC  
> Url: https://github.com/boostorg/exception/issues/55#issuecomment-1826953389  

@diehard2 I endorse using exceptions to handle errors. Boost LEAF works great with exceptions, you can think of it as a better Boost Exception. See https://www.boost.org/doc/libs/1_83_0/libs/leaf/doc/html/index.html#boost_exception.

---

## Comment 8

> Username: zlojvavan  
> Created at: 2023-11-27 11:50:41 UTC  
> Url: https://github.com/boostorg/exception/issues/55#issuecomment-1827682144  

@diehard2 despite not being designed to such customizations the library is still flexible enough to leave some backdoors allowing to implement required functionality through exploiting of boost::exception public methods and friend structs. in my mini-framework I replace standard boost implementation provided error info container with my own derived class providing needed methods. I believe you could do the same to achieve what you wish. so whenever my code throws new exception or rethrows caught exception thrown somewhere outside my control I install/replace my container into boost::exception instance copying already inserted error info from standard container into my one if any  
  
@zajo I was able to implement all required functionality without modifying/patching existing boost sources (though not in the most comfortable way) so not sure what changes would be acceptable for you. I already tried to ask you to enhance library in the past (as in https://github.com/boostorg/exception/issues/25) to no avail so I've had to find my own ways to achieve what I wanted then the way resembling @apolukhin approach from https://github.com/boostorg/stacktrace/pull/147

---

## Comment 9

> Username: zajo  
> Created at: 2023-11-27 20:06:55 UTC  
> Url: https://github.com/boostorg/exception/issues/55#issuecomment-1828509586  

@zlojvavan The issue you linked was about customizing throw exception, which I don't want to do. In this case it seems you have some kind of system for customizing the conversion of error_info to string, which could be useful, so I'm offering to take a look at it, since I'm not sure I understand the use case.

---

## Comment 10

> Username: zlojvavan  
> Created at: 2023-11-28 10:15:36 UTC  
> Url: https://github.com/boostorg/exception/issues/55#issuecomment-1829508801  

> it seems you have some kind of system for customizing the conversion of error_info to string  
  
well, yes I have rather primitive formatting capabilities. for example if in code above replace catch block with the following:  
	  
```  
	catch (...)  
	{  
		eh::handle_exception_params_t hep;  
		auto& dip = hep.diagnostic_info_params;  
		dip.nameFormat = "\"%s\": ";  
		dip.valueFormat = ">%s<";  
		dip.showTypeNames = false;  
		dip.types = V_EXCLUDE_DIAG_INFO_TYPES(eh::errinfo_file_name/*, eh::throw_chain, eh::throw_callstack, eh::errinfo_init_callstack, eh::errinfo_nested_exception*/);  
		/*hep.invokeCustomHandlers = true;  
		hep.getDiagnosticInfo = true;  
		hep.async = true;  
		auto& dup = hep.dup;  
		dup.handle = true;  
		dup.ignoreDuration = std::chrono::seconds{ 5 };  
		dup.useWhat = true;*/  
		HANDLE_EXCEPTION(hep);  
	}  
```  
it will change first part of produced report to  
  
```  
/opt/vcpkg/installed/x64-linux/include/boost/asio/ip/impl/address.ipp(112): Throw in function address boost::asio::ip::make_address(const char *)  
Dynamic exception type: vavan::eh::boosted_exception<boost::system::system_error>  
std::exception::what: Invalid argument [system:22]  
"system" error #22  
"some description": >rethrowing here<  
"variant": >1352345<  
"someVar": >1352345<  
"custom var description": >1352345<  
"API function name": >net::ip::make_address<  
"custom tuple description": >/home/vavan/.vs/test_all/test_all.cpp, 69, test, 5<  
"std::make_tuple("/home/vavan/.vs/test_all/test_all.cpp", 70, "test", 5)": >/home/vavan/.vs/test_all/test_all.cpp, 70, test, 5<  
"another tuple": >/home/vavan/.vs/test_all/test_all.cpp, 71, test, 5<  
"at line #": >72<  
```  
  
> I'm offering to take a look at it  
  
well, atm it doesn't exist as a patched/modified boost sources but is part of my "super header" containing lots of stuff I tend to use across different projects, so it may take indefinite time to produce PR containing only subset of available functionality in digestable form

---

## Comment 11

> Username: zajo  
> Created at: 2023-11-29 00:29:22 UTC  
> Url: https://github.com/boostorg/exception/issues/55#issuecomment-1830997491  

This makes me think that what you really need is conversion to json, and then you can parse it and do whatever you want with it.

---

## Comment 12

> Username: zlojvavan  
> Created at: 2023-11-29 07:01:33 UTC  
> Url: https://github.com/boostorg/exception/issues/55#issuecomment-1831324066  

@zajo well, for me there's no need to convert anything to json, ATM I'm fine with my exception handling and reporting stuff and been using it for years. there are still some inconveniences with throw sites that do not use boost::throw_exception though but with p2370 and some tricks as in mentioned above from_exception I hope we'll finally get there sooner than later

---

## Comment 13

> Username: zajo  
> Created at: 2023-11-30 20:38:49 UTC  
> Updated at: 2023-12-01 01:02:00 UTC  
> Url: https://github.com/boostorg/exception/issues/55#issuecomment-1834527636  

@zlojvavan LEAF is able to deal with throw sites not using boost::throw_exception.

---

## Comment 14

> Username: zlojvavan  
> Created at: 2023-12-05 08:31:26 UTC  
> Url: https://github.com/boostorg/exception/issues/55#issuecomment-1840258423  

@zajo do you mean it is able to get callstacks of all throw expressions throughout the entire application and all used rtl and thirdparty libs that do not use LEAF?  
  
anyway as already been explained several times switching libraries in existing codebases isn't always an option

---

## Comment 15

> Username: zajo  
> Created at: 2023-12-05 22:53:02 UTC  
> Url: https://github.com/boostorg/exception/issues/55#issuecomment-1841758414  

Yes, LEAF is able to do that. Switching from Boost Exception to Boost LEAF is very straightforward, even for a large codebase, I've done it in an afternoon. It doesn't mean that it's always a good idea, but it should be considered.
