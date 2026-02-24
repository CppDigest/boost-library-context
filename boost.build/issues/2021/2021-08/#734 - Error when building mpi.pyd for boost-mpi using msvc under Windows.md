# #734 - Error when building mpi.pyd for boost-mpi using msvc under Windows [Closed]

> Username: gnaggnoyil  
> Created at: 2021-08-31 08:26:36 UTC  
> Updated at: 2021-09-09 01:31:01 UTC  
> Closed at: 2021-09-09 01:31:01 UTC  
> Url: https://github.com/boostorg/build/issues/734  

### Environments:  
  
* Windows 10 19043.1165 with its bundled Powershell  
* Visual Studio 2019, cl.exe version 19.29.30133 x64  
  
### Steps to reproduce  
  
1. Get vcpkg (e.g. at location `C:\vcpkg`) and run `.\vcpkg.exe install mpi[*] python3[*] --triplet=x64-windows`  
2. Create a new `user-config.jam` file (e.g. located in `C:\dir1\user-config.jam`) with the following contents:  
    ```  
    import toolset ;  
    using msvc : 14.2 ;  
    using python : 3.9 :  
	    : "C:/vcpkg/installed/x64-windows/include/python3.9" : "C:/vcpkg/installed/x64-windows/lib" ;  
    using python : 3.9 :  
	    : "C:/vcpkg/installed/x64-windows/include/python3.9" : "C:/vcpkg/installed/x64-windows/debug/lib" : <python-debugging>on ;  
    using mpi : :  
	    <library-path>"C:/vcpkg/installed/x64-windows/lib"  
	    <include>"C:/vcpkg/installed/x64-windows/include"  
	    <find-shared-library>msmpi ;  
    ```  
3. Clone boost repository and all of its submodules.  
4. Open "x64 Native Tools Command Prompt for VS 2019" and then run powershell in it.  
5. In boost repo root directory, Run command `.\bootstrap.sh` and then run the following command:  
    ```  
    .\b2.exe -d+2 install --stagedir="C:\dir1\stage" --build-dir="C:\dir1" --project-config="project-config.jam" --user-config="C:\dir1\user-config.jam" --prefix="C:\dir1\.win_build\install" variant=release debug-symbols=off threading=multi runtime-link=shared link=shared toolset=msvc python=3.9 cxxstd=latest address-model=64 --layout=system -j1 -a  
    ```  
  
### Expected behavior  
  
No build error is reported during building  
  
### Actual behavior  
  
In console output, there are reports like the following:  
```  
        call "C:\dir1\boost\bin.v2\standalone\msvc\msvc-14.2\msvc-setup.bat"  >nul  
 link /NOLOGO /INCREMENTAL:NO "C:\dir1\boost\bin.v2\libs\mpi\build\msvc-14.2\release\cxxstd-latest-iso\python-3.9\threading-multi\python\collectives.obj" "C:\dir1\boost\bin.v2\libs\mpi\build\msvc-14.2\release\cxxstd-latest-iso\python-3.9\threading-multi\python\py_communicator.obj" "C:\dir1\boost\bin.v2\libs\mpi\build\msvc-14.2\release\cxxstd-latest-iso\python-3.9\threading-multi\python\datatypes.obj" "C:\dir1\boost\bin.v2\libs\mpi\build\msvc-14.2\release\cxxstd-latest-iso\python-3.9\threading-multi\python\documentation.obj" "C:\dir1\boost\bin.v2\libs\mpi\build\msvc-14.2\release\cxxstd-latest-iso\python-3.9\threading-multi\python\py_environment.obj" "C:\dir1\boost\bin.v2\libs\mpi\build\msvc-14.2\release\cxxstd-latest-iso\python-3.9\threading-multi\python\py_nonblocking.obj" "C:\dir1\boost\bin.v2\libs\mpi\build\msvc-14.2\release\cxxstd-latest-iso\python-3.9\threading-multi\python\py_exception.obj" "C:\dir1\boost\bin.v2\libs\mpi\build\msvc-14.2\release\cxxstd-latest-iso\python-3.9\threading-multi\python\module.obj" "C:\dir1\boost\bin.v2\libs\mpi\build\msvc-14.2\release\cxxstd-latest-iso\python-3.9\threading-multi\python\py_request.obj" "C:\dir1\boost\bin.v2\libs\mpi\build\msvc-14.2\release\cxxstd-latest-iso\python-3.9\threading-multi\python\skeleton_and_content.obj" "C:\dir1\boost\bin.v2\libs\mpi\build\msvc-14.2\release\cxxstd-latest-iso\python-3.9\threading-multi\python\status.obj" "C:\dir1\boost\bin.v2\libs\mpi\build\msvc-14.2\release\cxxstd-latest-iso\python-3.9\threading-multi\python\py_timer.obj" "C:\dir1\boost\bin.v2\libs\serialization\build\msvc-14.2\release\cxxstd-latest-iso\threading-multi\boost_serialization.lib" "C:\dir1\boost\bin.v2\libs\mpi\build\msvc-14.2\release\cxxstd-latest-iso\threading-multi\boost_mpi.lib" "C:\dir1\boost\bin.v2\libs\python\build\msvc-14.2\release\cxxstd-latest-iso\python-3.9\threading-multi\boost_python39.lib" "C:\dir1\boost\bin.v2\libs\mpi\build\msvc-14.2\release\cxxstd-latest-iso\python-3.9\threading-multi\boost_mpi_python39.lib"   "msmpi.lib" "python39.lib"  /NOENTRY /MACHINE:X64 /MANIFEST:EMBED /subsystem:console /out:"C:\dir1\boost\bin.v2\libs\mpi\build\msvc-14.2\release\cxxstd-latest-iso\python-3.9\threading-multi\mpi.pyd" /LIBPATH:"C:\Program Files (x86)\Microsoft SDKs\MPI\Lib\x64" /LIBPATH:"C:\Program Files (x86)\Microsoft SDKs\MPI\Lib\x86" /LIBPATH:"C:\vcpkg\installed\x64-windows\lib"  /DLL    
      
   Creating library C:\dir1\boost\bin.v2\libs\mpi\build\msvc-14.2\release\cxxstd-latest-iso\python-3.9\threading-multi\mpi.lib and object C:\dir1\boost\bin.v2\libs\mpi\build\msvc-14.2\release\cxxstd-latest-iso\python-3.9\threading-multi\mpi.exp  
py_request.obj : error LNK2001: unresolved external symbol _purecall  
skeleton_and_content.obj : error LNK2001: unresolved external symbol _purecall  
status.obj : error LNK2001: unresolved external symbol _purecall  
py_timer.obj : error LNK2001: unresolved external symbol _purecall  
collectives.obj : error LNK2001: unresolved external symbol _purecall  
py_communicator.obj : error LNK2001: unresolved external symbol _purecall  
py_nonblocking.obj : error LNK2001: unresolved external symbol _purecall  
py_exception.obj : error LNK2001: unresolved external symbol _purecall  
status.obj : error LNK2001: unresolved external symbol __std_terminate  
py_timer.obj : error LNK2001: unresolved external symbol __std_terminate  
py_nonblocking.obj : error LNK2001: unresolved external symbol __std_terminate  
py_exception.obj : error LNK2001: unresolved external symbol __std_terminate  
py_request.obj : error LNK2001: unresolved external symbol __std_terminate  
skeleton_and_content.obj : error LNK2001: unresolved external symbol __std_terminate  
collectives.obj : error LNK2001: unresolved external symbol __std_terminate  
py_communicator.obj : error LNK2001: unresolved external symbol __std_terminate  
datatypes.obj : error LNK2001: unresolved external symbol __std_terminate  
py_environment.obj : error LNK2001: unresolved external symbol __std_terminate  
py_exception.obj : error LNK2001: unresolved external symbol __imp__invalid_parameter_noinfo_noreturn  
collectives.obj : error LNK2001: unresolved external symbol __imp__invalid_parameter_noinfo_noreturn  
datatypes.obj : error LNK2001: unresolved external symbol __imp__invalid_parameter_noinfo_noreturn  
py_environment.obj : error LNK2001: unresolved external symbol __imp__invalid_parameter_noinfo_noreturn  
py_nonblocking.obj : error LNK2001: unresolved external symbol __imp__invalid_parameter_noinfo_noreturn  
status.obj : error LNK2001: unresolved external symbol __imp_calloc  
py_timer.obj : error LNK2001: unresolved external symbol __imp_calloc  
py_exception.obj : error LNK2001: unresolved external symbol __imp_calloc  
module.obj : error LNK2001: unresolved external symbol __imp_calloc  
py_request.obj : error LNK2001: unresolved external symbol __imp_calloc  
skeleton_and_content.obj : error LNK2001: unresolved external symbol __imp_calloc  
collectives.obj : error LNK2001: unresolved external symbol __imp_calloc  
py_communicator.obj : error LNK2001: unresolved external symbol __imp_calloc  
py_environment.obj : error LNK2001: unresolved external symbol __imp_calloc  
py_nonblocking.obj : error LNK2001: unresolved external symbol __imp_calloc  
py_exception.obj : error LNK2001: unresolved external symbol __std_exception_copy  
skeleton_and_content.obj : error LNK2001: unresolved external symbol __std_exception_copy  
MSVCRT.lib(throw_bad_alloc.obj) : error LNK2001: unresolved external symbol __std_exception_copy  
collectives.obj : error LNK2001: unresolved external symbol __std_exception_copy  
py_communicator.obj : error LNK2001: unresolved external symbol __std_exception_copy  
datatypes.obj : error LNK2001: unresolved external symbol __std_exception_copy  
py_nonblocking.obj : error LNK2001: unresolved external symbol __std_exception_copy  
py_exception.obj : error LNK2001: unresolved external symbol __std_exception_destroy  
skeleton_and_content.obj : error LNK2001: unresolved external symbol __std_exception_destroy  
MSVCRT.lib(throw_bad_alloc.obj) : error LNK2001: unresolved external symbol __std_exception_destroy  
collectives.obj : error LNK2001: unresolved external symbol __std_exception_destroy  
py_communicator.obj : error LNK2001: unresolved external symbol __std_exception_destroy  
datatypes.obj : error LNK2001: unresolved external symbol __std_exception_destroy  
py_nonblocking.obj : error LNK2001: unresolved external symbol __std_exception_destroy  
py_timer.obj : error LNK2001: unresolved external symbol __std_type_info_name  
py_exception.obj : error LNK2001: unresolved external symbol __std_type_info_name  
py_request.obj : error LNK2001: unresolved external symbol __std_type_info_name  
skeleton_and_content.obj : error LNK2001: unresolved external symbol __std_type_info_name  
status.obj : error LNK2001: unresolved external symbol __std_type_info_name  
collectives.obj : error LNK2001: unresolved external symbol __std_type_info_name  
py_communicator.obj : error LNK2001: unresolved external symbol __std_type_info_name  
py_environment.obj : error LNK2001: unresolved external symbol __std_type_info_name  
py_nonblocking.obj : error LNK2001: unresolved external symbol __std_type_info_name  
py_timer.obj : error LNK2001: unresolved external symbol _CxxThrowException  
MSVCRT.lib(throw_bad_alloc.obj) : error LNK2001: unresolved external symbol _CxxThrowException  
py_exception.obj : error LNK2001: unresolved external symbol _CxxThrowException  
py_request.obj : error LNK2001: unresolved external symbol _CxxThrowException  
skeleton_and_content.obj : error LNK2001: unresolved external symbol _CxxThrowException  
status.obj : error LNK2001: unresolved external symbol _CxxThrowException  
collectives.obj : error LNK2001: unresolved external symbol _CxxThrowException  
py_communicator.obj : error LNK2001: unresolved external symbol _CxxThrowException  
datatypes.obj : error LNK2001: unresolved external symbol _CxxThrowException  
py_nonblocking.obj : error LNK2001: unresolved external symbol _CxxThrowException  
skeleton_and_content.obj : error LNK2001: unresolved external symbol __CxxFrameHandler4  
status.obj : error LNK2001: unresolved external symbol __CxxFrameHandler4  
py_timer.obj : error LNK2001: unresolved external symbol __CxxFrameHandler4  
MSVCRT.lib(gshandlereh4.obj) : error LNK2001: unresolved external symbol __CxxFrameHandler4  
py_nonblocking.obj : error LNK2001: unresolved external symbol __CxxFrameHandler4  
py_exception.obj : error LNK2001: unresolved external symbol __CxxFrameHandler4  
module.obj : error LNK2001: unresolved external symbol __CxxFrameHandler4  
py_request.obj : error LNK2001: unresolved external symbol __CxxFrameHandler4  
collectives.obj : error LNK2001: unresolved external symbol __CxxFrameHandler4  
py_communicator.obj : error LNK2001: unresolved external symbol __CxxFrameHandler4  
datatypes.obj : error LNK2001: unresolved external symbol __CxxFrameHandler4  
py_environment.obj : error LNK2001: unresolved external symbol __CxxFrameHandler4  
status.obj : error LNK2001: unresolved external symbol memcpy  
py_timer.obj : error LNK2001: unresolved external symbol memcpy  
py_exception.obj : error LNK2001: unresolved external symbol memcpy  
module.obj : error LNK2001: unresolved external symbol memcpy  
py_request.obj : error LNK2001: unresolved external symbol memcpy  
skeleton_and_content.obj : error LNK2001: unresolved external symbol memcpy  
collectives.obj : error LNK2001: unresolved external symbol memcpy  
py_communicator.obj : error LNK2001: unresolved external symbol memcpy  
py_environment.obj : error LNK2001: unresolved external symbol memcpy  
py_nonblocking.obj : error LNK2001: unresolved external symbol memcpy  
collectives.obj : error LNK2019: unresolved external symbol memmove referenced in function "int * __cdecl std::_Copy_memmove<int *,int *>(int *,int *,int *)" (??$_Copy_memmove@PEAHPEAH@std@@YAPEAHPEAH00@Z)  
py_nonblocking.obj : error LNK2001: unresolved external symbol memmove  
py_exception.obj : error LNK2001: unresolved external symbol memmove  
MSVCRT.lib(utility_desktop.obj) : error LNK2001: unresolved external symbol memset  
collectives.obj : error LNK2001: unresolved external symbol memset  
py_nonblocking.obj : error LNK2001: unresolved external symbol memset  
py_exception.obj : error LNK2001: unresolved external symbol memset  
py_request.obj : error LNK2001: unresolved external symbol memset  
py_request.obj : error LNK2001: unresolved external symbol __std_type_info_compare  
skeleton_and_content.obj : error LNK2001: unresolved external symbol __std_type_info_compare  
status.obj : error LNK2001: unresolved external symbol __std_type_info_compare  
py_timer.obj : error LNK2001: unresolved external symbol __std_type_info_compare  
py_communicator.obj : error LNK2001: unresolved external symbol __std_type_info_compare  
datatypes.obj : error LNK2001: unresolved external symbol __std_type_info_compare  
py_nonblocking.obj : error LNK2001: unresolved external symbol __std_type_info_compare  
py_exception.obj : error LNK2001: unresolved external symbol __std_type_info_compare  
py_exception.obj : error LNK2019: unresolved external symbol __RTtypeid referenced in function "public: static struct std::pair<void *,struct boost::python::type_info> __cdecl boost::python::objects::polymorphic_id_generator<class boost::mpi::exception>::execute(void *)" (?execute@?$polymorphic_id_generator@Vexception@mpi@boost@@@objects@python@boost@@SA?AU?$pair@PEAXUtype_info@python@boost@@@std@@PEAX@Z)  
skeleton_and_content.obj : error LNK2001: unresolved external symbol __RTtypeid  
py_exception.obj : error LNK2019: unresolved external symbol __imp_free referenced in function "public: __cdecl std::_Tidy_guard<class std::numpunct<char> >::~_Tidy_guard<class std::numpunct<char> >(void)" (??1?$_Tidy_guard@V?$numpunct@D@std@@@std@@QEAA@XZ)  
py_exception.obj : error LNK2019: unresolved external symbol __imp_localeconv referenced in function "public: __cdecl std::numpunct<char>::numpunct<char>(class std::_Locinfo const &,unsigned __int64,bool)" (??0?$numpunct@D@std@@QEAA@AEBV_Locinfo@1@_K_N@Z)  
py_exception.obj : error LNK2019: unresolved external symbol __RTCastToVoid referenced in function "public: static struct std::pair<void *,struct boost::python::type_info> __cdecl boost::python::objects::polymorphic_id_generator<class boost::mpi::exception>::execute(void *)" (?execute@?$polymorphic_id_generator@Vexception@mpi@boost@@@objects@python@boost@@SA?AU?$pair@PEAXUtype_info@python@boost@@@std@@PEAX@Z)  
skeleton_and_content.obj : error LNK2001: unresolved external symbol __RTCastToVoid  
py_exception.obj : error LNK2019: unresolved external symbol memcmp referenced in function "public: bool __cdecl std::locale::operator==(class std::locale const &)const " (??8locale@std@@QEBA_NAEBV01@@Z)  
MSVCRT.lib(ehvecctr.obj) : error LNK2001: unresolved external symbol __C_specific_handler  
MSVCRT.lib(ehvccctr.obj) : error LNK2001: unresolved external symbol __C_specific_handler  
MSVCRT.lib(ehvecdtr.obj) : error LNK2001: unresolved external symbol __C_specific_handler  
MSVCRT.lib(utility.obj) : error LNK2001: unresolved external symbol __C_specific_handler  
MSVCRT.lib(ehvecdtr.obj) : error LNK2019: unresolved external symbol terminate referenced in function "int __cdecl ArrayUnwindFilter(struct _EXCEPTION_POINTERS *)" (?ArrayUnwindFilter@@YAHPEAU_EXCEPTION_POINTERS@@@Z)  
MSVCRT.lib(utility_desktop.obj) : error LNK2001: unresolved external symbol terminate  
MSVCRT.lib(ehvecdtr.obj) : error LNK2019: unresolved external symbol __current_exception referenced in function "int __cdecl ArrayUnwindFilter(struct _EXCEPTION_POINTERS *)" (?ArrayUnwindFilter@@YAHPEAU_EXCEPTION_POINTERS@@@Z)  
MSVCRT.lib(utility_desktop.obj) : error LNK2001: unresolved external symbol __current_exception  
MSVCRT.lib(ehvecdtr.obj) : error LNK2019: unresolved external symbol __current_exception_context referenced in function "int __cdecl ArrayUnwindFilter(struct _EXCEPTION_POINTERS *)" (?ArrayUnwindFilter@@YAHPEAU_EXCEPTION_POINTERS@@@Z)  
MSVCRT.lib(utility_desktop.obj) : error LNK2001: unresolved external symbol __current_exception_context  
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
MSVCRT.lib(gs_report.obj) : error LNK2019: unresolved external symbol __imp_GetCurrentProcess referenced in function __raise_securityfailure  
MSVCRT.lib(gs_report.obj) : error LNK2019: unresolved external symbol __imp_TerminateProcess referenced in function __raise_securityfailure  
C:\dir1\boost\bin.v2\libs\mpi\build\msvc-14.2\release\cxxstd-latest-iso\python-3.9\threading-multi\mpi.pyd : fatal error LNK1120: 68 unresolved externals  
...skipped <pC:\Users\gnaggnoyil\Documents\projects\boost\.win_build\install\lib\boost-python3.9>mpi.pyd for lack of <pC:\Users\gnaggnoyil\Documents\projects\boost\.win_build\boost\bin.v2\libs\mpi\build\msvc-14.2\release\cxxstd-latest-iso\python-3.9\threading-multi>mpi.pyd...  
```  
  
And in the end of the output there are reports like: `...failed updating 2 targets...`  
  
### Additional info  
  
I did some investigation and it seems PR #28 with commit 82d7b64 seems to be related with this issue. I do not quite understand the meaning of adding a `/NOENTRY` flag there. According to (msdn)[https://docs.microsoft.com/en-us/cpp/build/reference/noentry-no-entry-point?view=msvc-160], `/NOENTRY` is a flag that "is required for creating a resource-only DLL that contains no executable code" and will "prevent LINK from linking a reference to _main into the DLL", which seems to explain the above underfined reference error. I also found a (stackoverflow answer)[https://stackoverflow.com/a/54674881/2348830] that directly points this too. Does anyone have any ideas?

---

## Comment 1

> Username: pdimov  
> Created at: 2021-09-06 17:59:55 UTC  
> Url: https://github.com/boostorg/build/issues/734#issuecomment-913793468  

I confirm that `b2 --with-mpi link=shared runtime-link=shared variant=release python=3.9 address-model=64 stage` fails for me with the same link errors as above. Commenting out the line adding `/NOENTRY` in `msvc.jam` (https://github.com/boostorg/build/blob/ae1b579606d06b8d8900fe65fd63dffb46647588/src/tools/msvc.jam#L1989) fixes it. This line is activated by `python.jam` setting `suppress-import-lib` to `true` here: https://github.com/boostorg/build/blob/ae1b579606d06b8d8900fe65fd63dffb46647588/src/tools/python.jam#L1136.

---

## Comment 2

> Username: pdimov  
> Created at: 2021-09-06 18:10:20 UTC  
> Url: https://github.com/boostorg/build/issues/734#issuecomment-913797594  

The `python.jam` change that introduced this requirement is https://github.com/boostorg/build/commit/cd7aabdb8dbf3327bcfcdb8cc0de4c19bd393310, but it's of not much help.  
  
I suspect that this needs to be removed.

---

## Comment 3

> Username: gnaggnoyil  
> Created at: 2021-09-06 18:17:31 UTC  
> Url: https://github.com/boostorg/build/issues/734#issuecomment-913800155  

@pdimov Which one is to be removed? The `msvc.jam` one or the `python.jam` one?

---

## Comment 4

> Username: pdimov  
> Created at: 2021-09-06 18:24:52 UTC  
> Updated at: 2021-09-06 18:25:35 UTC  
> Url: https://github.com/boostorg/build/issues/734#issuecomment-913802732  

The `python.jam` one. `msvc.jam` does exactly as it's told.

---

## Comment 5

> Username: gnaggnoyil  
> Created at: 2021-09-06 18:28:21 UTC  
> Updated at: 2021-09-06 18:31:29 UTC  
> Url: https://github.com/boostorg/build/issues/734#issuecomment-913803937  

I see. So the idea is that python extensions don't need to do the "suppress import lib"-like things.

---

## Comment 6

> Username: pdimov  
> Created at: 2021-09-06 18:36:46 UTC  
> Url: https://github.com/boostorg/build/issues/734#issuecomment-913806538  

On second thought, maybe not.  
  
`suppress-import-lib` is specified in the B2 docs as "Suppresses creation of import library by the linker."  
  
This, taken literally, does not imply `/NOENTRY`. It implies `/NOIMPLIB`.  
  
I'm not sure what's the point of it, though.

---

## Comment 7

> Username: gnaggnoyil  
> Created at: 2021-09-06 19:19:11 UTC  
> Url: https://github.com/boostorg/build/issues/734#issuecomment-913820605  

I'm not sure if python extensions should do the "suppress import lib" things, but I don't think `/NOENTRY` is a suitable flag for doing the "suppress import lib" things. It's intended to create resource only dlls, as stated in msdn. There are also discussions in the original PR indicating "/NOENTRY would be wrong" for "a DLL that has executable code".

---

## Comment 8

> Username: pdimov  
> Created at: 2021-09-06 20:56:01 UTC  
> Url: https://github.com/boostorg/build/issues/734#issuecomment-913849851  

Yeah, I don't know what was the original purpose of using `/NOENTRY`. Maybe it was useful once, but now it obviously doesn't work.

---

## Comment 9

> Username: gnaggnoyil  
> Created at: 2021-09-07 07:30:54 UTC  
> Url: https://github.com/boostorg/build/issues/734#issuecomment-914062337  

I wonder if I could have contact @bernhard-b so that I could ask about what the original purpose was. Sadly I could not find any contact information of this person. :/
