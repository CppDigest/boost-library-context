# #619 - Link errors when running bootstrap.bat from ICC 19.1 command line in windows 10 [Open]

> Username: ajkerman  
> Created at: 2020-02-21 20:46:52 UTC  
> Updated at: 2021-05-29 17:22:36 UTC  
> Url: https://github.com/boostorg/build/issues/619  

I am trying to generate the Boost 1.72.0 libraries using the Intel C++ compiler 19.1 in windows 10, using the command:  
  
bootstrap intel-win32   
  
from the Boost root directory. Here are the errors in bootstrap.log:  
  
execnt.obj : error LNK2019: unresolved external symbol __imp_SetSecurityDescriptorDacl referenced in function "void __cdecl exec_cmd(struct string const *,int,void (__cdecl*)(void * const,int,struct timing_info const * const,char const * const,char const * const,int),void *,struct _list *)" (?exec_cmd@@YAXPEBUstring@@HP6AXQEAXHQEBUtiming_info@@QEBD3H@ZPEAXPEAU_list@@@Z)  
execnt.obj : error LNK2019: unresolved external symbol __imp_InitializeSecurityDescriptor referenced in function "void __cdecl exec_cmd(struct string const *,int,void (__cdecl*)(void * const,int,struct timing_info const * const,char const * const,char const * const,int),void *,struct _list *)" (?exec_cmd@@YAXPEBUstring@@HP6AXQEAXHQEBUtiming_info@@QEBD3H@ZPEAXPEAU_list@@@Z)  
execnt.obj : error LNK2019: unresolved external symbol __imp_EnumWindows referenced in function "void __cdecl exec_wait(void)" (?exec_wait@@YAXXZ)  
execnt.obj : error LNK2019: unresolved external symbol __imp_PostThreadMessageA referenced in function "int __cdecl close_alert_window_enum(struct HWND__ *,__int64)" (?close_alert_window_enum@@YAHPEAUHWND__@@_J@Z)  
execnt.obj : error LNK2019: unresolved external symbol __imp_PostMessageA referenced in function "int __cdecl close_alert_window_enum(struct HWND__ *,__int64)" (?close_alert_window_enum@@YAHPEAUHWND__@@_J@Z)  
execnt.obj : error LNK2019: unresolved external symbol __imp_GetWindowThreadProcessId referenced in function "int __cdecl close_alert_window_enum(struct HWND__ *,__int64)" (?close_alert_window_enum@@YAHPEAUHWND__@@_J@Z)  
execnt.obj : error LNK2019: unresolved external symbol __imp_GetClassNameA referenced in function "int __cdecl close_alert_window_enum(struct HWND__ *,__int64)" (?close_alert_window_enum@@YAHPEAUHWND__@@_J@Z)  
execnt.obj : error LNK2019: unresolved external symbol __imp_IsWindowVisible referenced in function "int __cdecl close_alert_window_enum(struct HWND__ *,__int64)" (?close_alert_window_enum@@YAHPEAUHWND__@@_J@Z)  
w32_getreg.obj : error LNK2019: unresolved external symbol __imp_RegCloseKey referenced in function "struct _list * __cdecl builtin_system_registry(struct frame *,int)" (?builtin_system_registry@@YAPEAU_list@@PEAUframe@@H@Z)  
w32_getreg.obj : error LNK2019: unresolved external symbol __imp_RegQueryValueExA referenced in function "struct _list * __cdecl builtin_system_registry(struct frame *,int)" (?builtin_system_registry@@YAPEAU_list@@PEAUframe@@H@Z)  
w32_getreg.obj : error LNK2019: unresolved external symbol __imp_RegOpenKeyExA referenced in function "struct _list * __cdecl builtin_system_registry(struct frame *,int)" (?builtin_system_registry@@YAPEAU_list@@PEAUframe@@H@Z)  
w32_getreg.obj : error LNK2019: unresolved external symbol __imp_RegEnumKeyExA referenced in function "struct _list * __cdecl builtin_system_registry_names(struct frame *,int)" (?builtin_system_registry_names@@YAPEAU_list@@PEAUframe@@H@Z)  
w32_getreg.obj : error LNK2019: unresolved external symbol __imp_RegEnumValueA referenced in function "struct _list * __cdecl builtin_system_registry_names(struct frame *,int)" (?builtin_system_registry_names@@YAPEAU_list@@PEAUframe@@H@Z)  
b2.exe : fatal error LNK1120: 13 unresolved externals  
  
help please?  
many thanks.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 17:22:06 UTC  
> Url: https://github.com/boostorg/build/issues/619#issuecomment-850868273  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
