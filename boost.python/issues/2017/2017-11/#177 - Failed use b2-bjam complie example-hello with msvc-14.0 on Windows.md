# #177 - Failed use b2/bjam complie example/hello with msvc-14.0 on Windows [Closed]

> Username: fasiondog  
> Created at: 2017-11-19 15:12:01 UTC  
> Updated at: 2018-06-20 14:34:25 UTC  
> Closed at: 2018-06-20 14:34:24 UTC  
> Url: https://github.com/boostorg/python/issues/177  

Hi,  
I'm use b2/bjam try to complie example("boost\libs\python\example\tutorial") with msvc-14.0 on Windows, but allways error LNK2019 and LNK2001, like:  
  
> hello.obj : error LNK2001: 无法解析的外部符号 __std_terminate  
> hello.obj : error LNK2019: 无法解析的外部符号 __imp___std_type_info_name，该符号在函数 "public: static struct boost::python::detail::signature_element const * __cdecl boost::python::detail::signature_arity<0>::impl<struct boost::mpl::vector1<char const *> >::elements(void)" (?elements@?$impl@U?$vector1@PEBD@mpl@boost@@@?$signature_arity@$0A@@detail@python@boost@@SAPEBUsignature_element@345@XZ) 中被引用  
> hello.obj : error LNK2001: 无法解析的外部符号 __CxxFrameHandler3  
> MSVCRT.lib(new_scalar.obj) : error LNK2019: 无法解析的外部符号 _callnewh，该符号在函数 "void * __cdecl operator new(unsigned __int64)" (??2@YAPEAX_K@Z) 中被引用  
> MSVCRT.lib(new_scalar.obj) : error LNK2019: 无法解析的外部符号 malloc，该符号在函数 "void * __cdecl operator new(unsigned __int64)" (??2@YAPEAX_K@Z) 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2019: 无法解析的外部符号 __vcrt_initialize，该符号在函数 __scrt_initialize_crt 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2019: 无法解析的外部符号 __vcrt_uninitialize，该符号在函数 __scrt_initialize_crt 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2019: 无法解析的外部符号 __vcrt_uninitialize_critical，该符号在函数 __scrt_dllmain_uninitialize_critical 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2019: 无法解析的外部符号 __vcrt_thread_attach，该符号在函数 __scrt_dllmain_crt_thread_attach 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2019: 无法解析的外部符号 __vcrt_thread_detach，该符号在函数 __scrt_dllmain_crt_thread_attach 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2019: 无法解析的外部符号 _seh_filter_dll，该符号在函数 __scrt_dllmain_exception_filter 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2019: 无法解析的外部符号 _is_c_termination_complete，该符号在函数 __scrt_dllmain_uninitialize_c 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2019: 无法解析的外部符号 _configure_narrow_argv，该符号在函数 "public: static int __cdecl __scrt_narrow_argv_policy::configure_argv(void)" (?configure_argv@__scrt_narrow_argv_policy@@SAHXZ) 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2019: 无法解析的外部符号 _initialize_narrow_environment，该符号在函数 __scrt_dllmain_after_initialize_c 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2019: 无法解析的外部符号 _initialize_onexit_table，该符号在函数 __scrt_initialize_onexit_tables 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2019: 无法解析的外部符号 _register_onexit_function，该符号在函数 _onexit 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2019: 无法解析的外部符号 _execute_onexit_table，该符号在函数 __scrt_dllmain_uninitialize_c 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2019: 无法解析的外部符号 _crt_atexit，该符号在函数 _onexit 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2019: 无法解析的外部符号 _crt_at_quick_exit，该符号在函数 at_quick_exit 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2019: 无法解析的外部符号 __acrt_initialize，该符号在函数 __scrt_initialize_crt 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2019: 无法解析的外部符号 __acrt_uninitialize，该符号在函数 __scrt_uninitialize_crt 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2019: 无法解析的外部符号 __acrt_uninitialize_critical，该符号在函数 __scrt_dllmain_uninitialize_critical 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2019: 无法解析的外部符号 __acrt_thread_attach，该符号在函数 __scrt_dllmain_crt_thread_attach 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2019: 无法解析的外部符号 __acrt_thread_detach，该符号在函数 __scrt_dllmain_crt_thread_detach 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2019: 无法解析的外部符号 _cexit，该符号在函数 __scrt_dllmain_uninitialize_c 中被引用  
> MSVCRT.lib(utility.obj) : error LNK2001: 无法解析的外部符号 __C_specific_handler  
> MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: 无法解析的外部符号 __imp_CloseHandle，该符号在函数 "void __cdecl __scrt_uninitialize_thread_safe_statics(void)" (?__scrt_uninitialize_thread_safe_statics@@YAXXZ) 中被引用  
> MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: 无法解析的外部符号 __imp_EnterCriticalSection，该符号在函数 _Init_thread_abort 中被引用  
> MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: 无法解析的外部符号 __imp_LeaveCriticalSection，该符号在函数 _Init_thread_abort 中被引用  
> MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: 无法解析的外部符号 __imp_DeleteCriticalSection，该符号在函数 "void __cdecl __scrt_uninitialize_thread_safe_statics(void)" (?__scrt_uninitialize_thread_safe_statics@@YAXXZ) 中被引用  
> MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: 无法解析的外部符号 __imp_SetEvent，该符号在函数 _Init_thread_notify 中被引用  
> MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: 无法解析的外部符号 __imp_ResetEvent，该符号在函数 _Init_thread_notify 中被引用  
> MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: 无法解析的外部符号 __imp_WaitForSingleObjectEx，该符号在函数 _Init_thread_wait 中被引用  
> MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: 无法解析的外部符号 __imp_CreateEventW，该符号在函数 "int __cdecl __scrt_initialize_thread_safe_statics(void)" (?__scrt_initialize_thread_safe_statics@@YAHXZ) 中被引用  
> MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: 无法解析的外部符号 __imp_GetModuleHandleW，该符号在函数 "int __cdecl __scrt_initialize_thread_safe_statics(void)" (?__scrt_initialize_thread_safe_statics@@YAHXZ) 中被引用  
> MSVCRT.lib(utility_desktop.obj) : error LNK2001: 无法解析的外部符号 __imp_GetModuleHandleW  
> MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: 无法解析的外部符号 __imp_GetProcAddress，该符号在函数 "int __cdecl __scrt_initialize_thread_safe_statics(void)" (?__scrt_initialize_thread_safe_statics@@YAHXZ) 中被引用  
> MSVCRT.lib(thread_safe_statics.obj) : error LNK2019: 无法解析的外部符号 __vcrt_InitializeCriticalSectionEx，该符号在函数 "int __cdecl __scrt_initialize_thread_safe_statics(void)" (?__scrt_initialize_thread_safe_statics@@YAHXZ) 中被引用  
> MSVCRT.lib(tncleanup.obj) : error LNK2019: 无法解析的外部符号 __imp_InitializeSListHead，该符号在函数 "void __cdecl __scrt_initialize_type_info(void)" (?__scrt_initialize_type_info@@YAXXZ) 中被引用  
> MSVCRT.lib(tncleanup.obj) : error LNK2019: 无法解析的外部符号 __std_type_info_destroy_list，该符号在函数 "void __cdecl __scrt_uninitialize_type_info(void)" (?__scrt_uninitialize_type_info@@YAXXZ) 中被引用  
> MSVCRT.lib(throw_bad_alloc.obj) : error LNK2019: 无法解析的外部符号 __std_exception_copy，该符号在函数 "public: __cdecl std::bad_alloc::bad_alloc(class std::bad_alloc const &)" (??0bad_alloc@std@@QEAA@AEBV01@@Z) 中被引用  
> MSVCRT.lib(throw_bad_alloc.obj) : error LNK2019: 无法解析的外部符号 __std_exception_destroy，该符号在函数 "public: virtual __cdecl std::bad_alloc::~bad_alloc(void)" (??1bad_alloc@std@@UEAA@XZ) 中被引用  
> MSVCRT.lib(throw_bad_alloc.obj) : error LNK2019: 无法解析的外部符号 _CxxThrowException，该符号在函数 "void __cdecl __scrt_throw_std_bad_alloc(void)" (?__scrt_throw_std_bad_alloc@@YAXXZ) 中被引用  
> MSVCRT.lib(delete_scalar.obj) : error LNK2019: 无法解析的外部符号 free，该符号在函数 "void __cdecl operator delete(void *)" (??3@YAXPEAX@Z) 中被引用  
> MSVCRT.lib(utility_desktop.obj) : error LNK2019: 无法解析的外部符号 __imp_RtlCaptureContext，该符号在函数 __scrt_fastfail 中被引用  
> MSVCRT.lib(utility_desktop.obj) : error LNK2019: 无法解析的外部符号 __imp_RtlLookupFunctionEntry，该符号在函数 __scrt_fastfail 中被引用  
> MSVCRT.lib(utility_desktop.obj) : error LNK2019: 无法解析的外部符号 __imp_RtlVirtualUnwind，该符号在函数 __scrt_fastfail 中被引用  
> MSVCRT.lib(utility_desktop.obj) : error LNK2019: 无法解析的外部符号 __imp_IsDebuggerPresent，该符号在函数 __scrt_fastfail 中被引用  
> MSVCRT.lib(utility_desktop.obj) : error LNK2019: 无法解析的外部符号 __imp_UnhandledExceptionFilter，该符号在函数 __scrt_fastfail 中被引用  
> MSVCRT.lib(utility_desktop.obj) : error LNK2019: 无法解析的外部符号 __imp_SetUnhandledExceptionFilter，该符号在函数 __scrt_fastfail 中被引用  
> MSVCRT.lib(utility_desktop.obj) : error LNK2019: 无法解析的外部符号 __imp_GetStartupInfoW，该符号在函数 __scrt_get_show_window_mode 中被引用  
> MSVCRT.lib(utility_desktop.obj) : error LNK2019: 无法解析的外部符号 IsProcessorFeaturePresent，该符号在函数 __scrt_fastfail 中被引用  
> MSVCRT.lib(utility_desktop.obj) : error LNK2019: 无法解析的外部符号 terminate，该符号在函数 __scrt_unhandled_exception_filter 中被引用  
> MSVCRT.lib(utility_desktop.obj) : error LNK2019: 无法解析的外部符号 memset，该符号在函数 __scrt_fastfail 中被引用  
> hello_ext.pyd : fatal error LNK1120: 53 个无法解析的外部命令  
>   
>         call "C:\Users\ADMINI~1\AppData\Local\Temp\b2_msvc_14.0_vcvarsall_amd64.cmd" >nul  
> link /NOLOGO /INCREMENTAL:NO /DLL /NOENTRY /MACHINE:X64 /MANIFEST /subsystem:console /out:"hello_ext.pyd"  /LIBPATH:"C:\Anaconda3\libs"   @"hello_ext.pyd.rsp"  
>         if %ERRORLEVEL% NEQ 0 EXIT %ERRORLEVEL%  
>       
> ...failed msvc.link.dll hello_ext.pyd...  
> ...skipped <p.>hello for lack of <p.>hello_ext.pyd...  
> ...failed updating 1 target...  
> ...skipped 2 targets...  
> ...updated 30 targets...  
  
I can create Visual Studio project, it is OK.  So, I try to add library in Jamfile, like:  
  
> python-extension hello_ext : hello.cpp  
>                            "C:\\Program Files (x86)\\Windows Kits\\8.1\\Lib\\winv6.3\\um\\x64\\kernel32.lib"  
>                            "C:\\Program Files (x86)\\Microsoft Visual Studio 14.0\\VC\\lib\\amd64\\msvcrt.lib"  
>                            "C:\\Program Files (x86)\\Microsoft Visual Studio 14.0\\VC\\lib\\amd64\\vcruntime.lib"  
>                            ;  
  
"fatal error LNK1120: 53 missing symbols" can down 22.  
  
I'm always using b2/bjam project that is so convenient to me. I google long time, but not fount any anwser. So I try to submit issue.  
  
I'm Jamroot use python3.5:  
  
> using python : 3.5 : C:\\Anaconda3 : C:\\Anaconda3\\include : C:\\Anaconda3\\libs ;  
  
Thanks

---

## Comment 1

> Username: fasiondog  
> Created at: 2018-06-20 14:34:24 UTC  
> Url: https://github.com/boostorg/python/issues/177#issuecomment-398771963  

Sorry, I found out why that is failed to install WinSDK with VC2015. So I am uninstalled VC2015 and reinstall until can see WinSDK in my harddisk.
