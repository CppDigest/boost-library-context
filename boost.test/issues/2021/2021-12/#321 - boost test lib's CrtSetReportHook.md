# #321 - boost test lib's CrtSetReportHook [Open]

> Username: mohvam  
> Created at: 2021-12-13 05:03:25 UTC  
> Updated at: 2022-02-27 01:44:17 UTC  
> Url: https://github.com/boostorg/test/issues/321  

Hi,  
        While executing one of our tests which throws an exception we encountered an issue where the uncaught exception invokes terminate(). We've a terminate_handler registered via std::set_terminate() which writes a core-dump and then invokes abort(). However the process does not exit but continues with invoking terminate() again with the following repeating until it invokes the terminate_handler which again creates another core-dump.  
          
          
  
>  	boost_test-w64d-21-3.dll!boost::unit_test::ut_detail::throw_exception<boost::execution_exception>(const boost::execution_exception & e={...}) Line 48	C++  
 	boost_test-w64d-21-3.dll!boost::detail::report_error(boost::execution_exception::error_code ec=system_error, const boost::exception * be=0x0000000000000000, const char * format=0x0000000003e137f0, char * * args=0x0000000003e13698) Line 261	C++  
 	boost_test-w64d-21-3.dll!boost::detail::report_error(boost::execution_exception::error_code ec=system_error, const char * format=0x0000000003e137f0, ...) Line 291	C++  
 	boost_test-w64d-21-3.dll!boost::detail::assert_reporting_function(int reportType=1, char * userMessage=0x0000000003e137f0, int * __formal=0x0000000003e13758) Line 1135	C++  
 	ucrtbased.dll!_VCrtDbgReportW(int nRptType=1, void * returnAddress=0x000007fedf757461, const wchar_t * szFile=0x0000000000000000, int nLine=0, const wchar_t * szModule=0x0000000000000000, const wchar_t * szFormat=0x000007fedf891c30, char * arglist=0x0000000003e1b898) Line 613	C++  
 	ucrtbased.dll!_CrtDbgReportW(int report_type=1, const wchar_t * file_name=0x0000000000000000, int line_number=0, const wchar_t * module_name=0x0000000000000000, const wchar_t * format=0x000007fedf891c30, ...) Line 278	C++  
 	ucrtbased.dll!issue_debug_notification(const wchar_t * const message=0x000007fedf86d3e0) Line 25	C++  
 	ucrtbased.dll!__acrt_report_runtime_error(const wchar_t * message=0x000007fedf86d3e0) Line 154	C++  
 	ucrtbased.dll!abort() Line 61	C++  
 	ucrtbased.dll!terminate() Line 59	C++  
 	VCRUNTIME140_1D.dll!FindHandler<__FrameHandler4>(EHExceptionRecord * pExcept=0x0000000003e1cdc0, unsigned __int64 * pRN=0x0000000003e1bcb0, _CONTEXT * pContext=0x0000000003e1c790, _xDISPATCHER_CONTEXT * pDC=0x0000000003e1bda0, FH4::FuncInfo4 * pFuncInfo=0x0000000003e1bcb8, unsigned char recursive='\0', int CatchDepth=0, unsigned __int64 * pMarkerRN=0x0000000000000000) Line 781	C++  
 	VCRUNTIME140_1D.dll!__InternalCxxFrameHandler<__FrameHandler4>(EHExceptionRecord * pExcept=0x0000000003e1cdc0, unsigned __int64 * pRN=0x0000000003e1bcb0, _CONTEXT * pContext=0x0000000003e1c790, _xDISPATCHER_CONTEXT * pDC=0x0000000003e1bda0, FH4::FuncInfo4 * pFuncInfo=0x0000000003e1bcb8, int CatchDepth=0, unsigned __int64 * pMarkerRN=0x0000000000000000, unsigned char recursive='\0') Line 355	C++  
 	VCRUNTIME140_1D.dll!__CxxFrameHandler4(EHExceptionRecord * pExcept=0x0000000003e1cdc0, unsigned __int64 RN=65130704, _CONTEXT * pContext=0x0000000003e1c790, _xDISPATCHER_CONTEXT * pDC=0x0000000003e1bda0) Line 304	C++  
 	ntdll.dll!RtlpExecuteHandlerForException()	Unknown  
 	ntdll.dll!RtlDispatchException()	Unknown  
 	ntdll.dll!RtlRaiseException()	Unknown  
 	KERNELBASE.dll!RaiseException()	Unknown  
 	VCRUNTIME140D.dll!_CxxThrowException(void * pExceptionObject=0x0000000003e1cf20, const _s__ThrowInfo * pThrowInfo=0x000007fee26ea7f0) Line 75	C++  
  
  
We've attached the final stack. As can be seen in that it continues with the above stack repeating itself - (and each repetition thereby creating one core dump).  
For investigating this issue further, we've invoked CrtSetReportHook() in our code. After this the process was seen exiting properly. Could you please have a look and let us know as to why the boot test library's report-hook would cause the above looping ?  
  
[exception.txt](https://github.com/boostorg/test/files/7701102/exception.txt)

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2022-02-27 01:44:05 UTC  
> Url: https://github.com/boostorg/test/issues/321#issuecomment-1052925708  

From this it is very hard to understand what is going wrong with your program. All I can say is that on some systems, Boost.Test capture the `SIGABRT` and continues on the next test.
