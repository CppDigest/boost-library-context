# #357 - Boost\libs\python failed to build due to link error on MSVC [Open]

> Username: QuellaZhang  
> Created at: 2021-03-10 07:52:30 UTC  
> Updated at: 2021-03-10 12:15:40 UTC  
> Url: https://github.com/boostorg/python/issues/357  

**Issue description:**  
Boost\libs\python failed to build due to link error on MSVC. Could you please take a look?  
  
**Build step**:  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2019 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\python\test  
  
**Error info:**  
F:\gitP\boostorg\boost\linkrepro>link @link.rsp  
   Creating library .\builtin_converters_ext.lib and object .\builtin_converters_ext.exp  
builtin_converters.obj : error LNK2001: unresolved external symbol __std_terminate  
builtin_converters.obj : error LNK2019: unresolved external symbol __imp__invalid_parameter_noinfo_noreturn referenced in function "void * __cdecl std::_Allocate_manually_vector_aligned<struct std::_Default_allocate_traits>(unsigned __int64)" (??$_Allocate_manually_vector_aligned@U_Default_allocate_traits@std@@@std@@YAPEAX_K@Z)  
builtin_converters.obj : error LNK2019: unresolved external symbol __std_exception_copy referenced in function "public: __cdecl std::bad_alloc::bad_alloc(class std::bad_alloc const &)" (??0bad_alloc@std@@QEAA@AEBV01@@Z)  
MSVCRT.lib(throw_bad_alloc.obj) : error LNK2001: unresolved external symbol __std_exception_copy  
builtin_converters.obj : error LNK2019: unresolved external symbol __std_exception_destroy referenced in function "public: virtual __cdecl std::bad_array_new_length::~bad_array_new_length(void)" (??1bad_array_new_length@std@@UEAA@XZ)  
MSVCRT.lib(throw_bad_alloc.obj) : error LNK2001: unresolved external symbol __std_exception_destroy  
builtin_converters.obj : error LNK2019: unresolved external symbol __std_type_info_name referenced in function "struct boost::python::detail::signature_element const * __cdecl boost::python::detail::get_ret<struct boost::python::default_call_policies,struct boost::mpl::vector1<int> >(void)" (??$get_ret@Udefault_call_policies@python@boost@@U?$vector1@H@mpl@3@@detail@python@boost@@YAPEBUsignature_element@012@XZ)  
builtin_converters.obj : error LNK2019: unresolved external symbol _CxxThrowException referenced in function "void __cdecl std::_Throw_bad_array_new_length(void)" (?_Throw_bad_array_new_length@std@@YAXXZ)  
MSVCRT.lib(throw_bad_alloc.obj) : error LNK2001: unresolved external symbol _CxxThrowException  
builtin_converters.obj : error LNK2001: unresolved external symbol __CxxFrameHandler4  
MSVCRT.lib(gshandlereh4.obj) : error LNK2001: unresolved external symbol __CxxFrameHandler4  
builtin_converters.obj : error LNK2019: unresolved external symbol memcpy referenced in function "public: __cdecl std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >(class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const &)" (??0?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@@std@@QEAA@AEBV01@@Z)  
MSVCRT.lib(new_scalar.obj) : error LNK2019: unresolved external symbol _callnewh referenced in function "void * __cdecl operator new(unsigned __int64)" (??2@YAPEAX_K@Z)  
MSVCRT.lib(new_scalar.obj) : error LNK2019: unresolved external symbol malloc referenced in function "void * __cdecl operator new(unsigned __int64)" (??2@YAPEAX_K@Z)  
MSVCRT.lib(utility.obj) : error LNK2019: unresolved external symbol __vcrt_initialize referenced in function __scrt_initialize_crt  
MSVCRT.lib(utility.obj) : error LNK2019: unresolved external symbol __vcrt_uninitialize referenced in function __scrt_initialize_crt  
MSVCRT.lib(utility.obj) : error LNK2019: unresolved external symbol __vcrt_uninitialize_critical referenced in function __scrt_dllmain_uninitialize_critical  
MSVCRT.lib(utility.obj) : error LNK2019: unresolved external symbol __vcrt_thread_attach referenced in function __scrt_dllmain_crt_thread_attach  
MSVCRT.lib(utility.obj) : error LNK2019: unresolved external symbol __vcrt_thread_detach referenced in function __scrt_dllmain_crt_thread_attach  
MSVCRT.lib(utility.obj) : error LNK2019: unresolved external symbol _seh_filter_dll referenced in function __scrt_dllmain_exception_filter  
MSVCRT.lib(utility.obj) : error LNK2019: unresolved external symbol _is_c_termination_complete referenced in function __scrt_dllmain_uninitialize_c  
MSVCRT.lib(utility.obj) : error LNK2019: unresolved external symbol _configure_narrow_argv referenced in function "public: static int __cdecl __scrt_narrow_argv_policy::configure_argv(void)" (?configure_argv@__scrt_narrow_argv_policy@@SAHXZ)  
MSVCRT.lib(utility.obj) : error LNK2019: unresolved external symbol _initialize_narrow_environment referenced in function "public: static int __cdecl __scrt_narrow_environment_policy::initialize_environment(void)" (?initialize_environment@__scrt_narrow_environment_policy@@SAHXZ)  
MSVCRT.lib(utility.obj) : error LNK2019: unresolved external symbol _initialize_onexit_table referenced in function __scrt_initialize_onexit_tables  
MSVCRT.lib(utility.obj) : error LNK2019: unresolved external symbol _register_onexit_function referenced in function _onexit  
MSVCRT.lib(utility.obj) : error LNK2019: unresolved external symbol _execute_onexit_table referenced in function __scrt_dllmain_uninitialize_c  
MSVCRT.lib(utility.obj) : error LNK2019: unresolved external symbol _crt_atexit referenced in function _onexit  
MSVCRT.lib(utility.obj) : error LNK2019: unresolved external symbol _crt_at_quick_exit referenced in function at_quick_exit  
MSVCRT.lib(utility.obj) : error LNK2019: unresolved external symbol __acrt_initialize referenced in function __scrt_initialize_crt  
MSVCRT.lib(utility.obj) : error LNK2019: unresolved external symbol __acrt_uninitialize referenced in function __scrt_uninitialize_crt  
MSVCRT.lib(utility.obj) : error LNK2019: unresolved external symbol __acrt_uninitialize_critical referenced in function __scrt_dllmain_uninitialize_critical  
MSVCRT.lib(utility.obj) : error LNK2019: unresolved external symbol __acrt_thread_attach referenced in function __scrt_dllmain_crt_thread_attach  
MSVCRT.lib(utility.obj) : error LNK2019: unresolved external symbol __acrt_thread_detach referenced in function __scrt_dllmain_crt_thread_detach  
MSVCRT.lib(utility.obj) : error LNK2019: unresolved external symbol _cexit referenced in function __scrt_dllmain_uninitialize_c  
MSVCRT.lib(utility.obj) : error LNK2001: unresolved external symbol __C_specific_handler  
MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: unresolved external symbol __imp_CloseHandle referenced in function "void __cdecl __scrt_uninitialize_thread_safe_statics(void)" (?__scrt_uninitialize_thread_safe_statics@@YAXXZ)  
MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: unresolved external symbol __imp_EnterCriticalSection referenced in function _Init_thread_abort  
MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: unresolved external symbol __imp_LeaveCriticalSection referenced in function _Init_thread_abort  
MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: unresolved external symbol __imp_InitializeCriticalSectionAndSpinCount referenced in function "int __cdecl __scrt_initialize_thread_safe_statics(void)" (?__scrt_initialize_thread_safe_statics@@YAHXZ)  
MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: unresolved external symbol __imp_DeleteCriticalSection referenced in function "void __cdecl __scrt_uninitialize_thread_safe_statics(void)" (?__scrt_uninitialize_thread_safe_statics@@YAXXZ)  
MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: unresolved external symbol __imp_SetEvent referenced in function _Init_thread_notify  
MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: unresolved external symbol __imp_ResetEvent referenced in function _Init_thread_notify  
MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: unresolved external symbol __imp_WaitForSingleObjectEx referenced in function _Init_thread_wait  
MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: unresolved external symbol __imp_CreateEventW referenced in function "int __cdecl __scrt_initialize_thread_safe_statics(void)" (?__scrt_initialize_thread_safe_statics@@YAHXZ)  
MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: unresolved external symbol __imp_GetModuleHandleW referenced in function "int __cdecl __scrt_initialize_thread_safe_statics(void)" (?__scrt_initialize_thread_safe_statics@@YAHXZ)  
MSVCRT.lib(utility_desktop.obj) : error LNK2001: unresolved external symbol __imp_GetModuleHandleW  
MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: unresolved external symbol __imp_GetProcAddress referenced in function "int __cdecl __scrt_initialize_thread_safe_statics(void)" (?__scrt_initialize_thread_safe_statics@@YAHXZ)  
MSVCRT.lib(tncleanup.obj) : error LNK2019: unresolved external symbol __imp_InitializeSListHead referenced in function "void __cdecl __scrt_initialize_type_info(void)" (?__scrt_initialize_type_info@@YAXXZ)  
MSVCRT.lib(tncleanup.obj) : error LNK2019: unresolved external symbol __std_type_info_destroy_list referenced in function "void __cdecl __scrt_uninitialize_type_info(void)" (?__scrt_uninitialize_type_info@@YAXXZ)  
MSVCRT.lib(delete_scalar.obj) : error LNK2019: unresolved external symbol free referenced in function "void __cdecl operator delete(void *)" (??3@YAXPEAX@Z)  
MSVCRT.lib(utility_desktop.obj) : error LNK2019: unresolved external symbol __imp_RtlCaptureContext referenced in function __scrt_fastfail  
MSVCRT.lib(gs_report.obj) : error LNK2001: unresolved external symbol __imp_RtlCaptureContext  
MSVCRT.lib(utility_desktop.obj) : error LNK2019: unresolved external symbol __imp_RtlLookupFunctionEntry referenced in function __scrt_fastfail  
MSVCRT.lib(gs_report.obj) : error LNK2001: unresolved external symbol __imp_RtlLookupFunctionEntry  
MSVCRT.lib(utility_desktop.obj) : error LNK2019: unresolved external symbol __imp_RtlVirtualUnwind referenced in function __scrt_fastfail  
MSVCRT.lib(gs_report.obj) : error LNK2001: unresolved external symbol __imp_RtlVirtualUnwind  
MSVCRT.lib(utility_desktop.obj) : error LNK2019: unresolved external symbol __imp_IsDebuggerPresent referenced in function __scrt_fastfail  
MSVCRT.lib(utility_desktop.obj) : error LNK2019: unresolved external symbol __imp_UnhandledExceptionFilter referenced in function __scrt_fastfail  
MSVCRT.lib(gs_report.obj) : error LNK2001: unresolved external symbol __imp_UnhandledExceptionFilter  
MSVCRT.lib(utility_desktop.obj) : error LNK2019: unresolved external symbol __imp_SetUnhandledExceptionFilter referenced in function __scrt_fastfail  
MSVCRT.lib(gs_report.obj) : error LNK2001: unresolved external symbol __imp_SetUnhandledExceptionFilter  
MSVCRT.lib(utility_desktop.obj) : error LNK2019: unresolved external symbol __imp_GetStartupInfoW referenced in function __scrt_get_show_window_mode  
MSVCRT.lib(utility_desktop.obj) : error LNK2019: unresolved external symbol __imp_IsProcessorFeaturePresent referenced in function __scrt_fastfail  
MSVCRT.lib(gs_report.obj) : error LNK2001: unresolved external symbol __imp_IsProcessorFeaturePresent  
MSVCRT.lib(utility_desktop.obj) : error LNK2019: unresolved external symbol terminate referenced in function __scrt_unhandled_exception_filter  
MSVCRT.lib(utility_desktop.obj) : error LNK2019: unresolved external symbol __current_exception referenced in function __scrt_unhandled_exception_filter  
MSVCRT.lib(utility_desktop.obj) : error LNK2019: unresolved external symbol __current_exception_context referenced in function __scrt_unhandled_exception_filter  
MSVCRT.lib(utility_desktop.obj) : error LNK2019: unresolved external symbol memset referenced in function __scrt_fastfail  
MSVCRT.lib(gs_report.obj) : error LNK2019: unresolved external symbol __imp_GetCurrentProcess referenced in function __raise_securityfailure  
MSVCRT.lib(gs_report.obj) : error LNK2019: unresolved external symbol __imp_TerminateProcess referenced in function __raise_securityfailure  
.\builtin_converters_ext.pyd : fatal error LNK1120: 59 unresolved externals

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2021-03-10 12:15:40 UTC  
> Url: https://github.com/boostorg/python/issues/357#issuecomment-795331708  

The missing symbols appear to be coming at least in part from the C++ standard library. Please ask on the main boost mailing list, or on the `Boost.Build` project, as this is a build configuration issue.
