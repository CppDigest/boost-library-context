# #136 - AV in debugging_symbols::get_source_file_line_impl during app finalization [Closed]

> Username: zlojvavan  
> Created at: 2023-04-05 10:49:01 UTC  
> Updated at: 2024-07-16 05:47:24 UTC  
> Closed at: 2024-07-16 05:47:23 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/136  

boost 1.81 from vcpkg on windows  
  
"Copy Details" button in "Exception Thrown" window in VS 2022 as usually cannot open clipboard so please bear with me for bringing screenshot  
  
![image](https://user-images.githubusercontent.com/23330634/230058431-99b49f74-c687-4b66-bc2b-6b6d7ddf9e9d.png)  
  
tried to output stacktrace to stream during late destructor (apparently too late to idebug_)  
here's meaningful excerpt from the callstack from IDE  
  
>	throw_exception_spec.exe!boost::stacktrace::detail::debugging_symbols::get_source_file_line_impl(const void * addr=0x000000013f6dd1db) Line 262	C++  
 	throw_exception_spec.exe!boost::stacktrace::detail::debugging_symbols::to_string_impl(const void * addr=0x000000013f6dd1db, std::string & res={...}) Line 314	C++  
 	throw_exception_spec.exe!boost::stacktrace::detail::to_string(const boost::stacktrace::frame * frames=0x0000000005cd5eb8, unsigned __int64 size=31) Line 343	C++  
....  
 	throw_exception_spec.exe!_execute_onexit_table::__l2::<lambda>() Line 206	C++  
 	throw_exception_spec.exe!__crt_seh_guarded_call<int>::operator()<void <lambda>(void),int <lambda>(void) &,void <lambda>(void)>(__acrt_lock_and_call::__l2::void <lambda>(void) && setup=void <lambda>(void){...}, _execute_onexit_table::__l2::int <lambda>(void) & action=int <lambda>(void){...}, __acrt_lock_and_call::__l2::void <lambda>(void) && cleanup=void <lambda>(void){...}) Line 204	C++  
 	throw_exception_spec.exe!__acrt_lock_and_call<int <lambda>(void)>(const __acrt_lock_id lock_id=__acrt_exit_lock, _execute_onexit_table::__l2::int <lambda>(void) && action=int <lambda>(void){...}) Line 974	C++  
 	throw_exception_spec.exe!_execute_onexit_table(_onexit_table_t * table=0x000000013fd11500) Line 231	C++  
 	throw_exception_spec.exe!common_exit::__l2::<lambda>() Line 227	C++  
 	throw_exception_spec.exe!__crt_seh_guarded_call<void>::operator()<void <lambda>(void),void <lambda>(void) &,void <lambda>(void)>(__acrt_lock_and_call::__l2::void <lambda>(void) && setup=void <lambda>(void){...}, common_exit::__l2::void <lambda>(void) & action=void <lambda>(void){...}, __acrt_lock_and_call::__l2::void <lambda>(void) && cleanup=void <lambda>(void){...}) Line 224	C++  
 	throw_exception_spec.exe!__acrt_lock_and_call<void <lambda>(void)>(const __acrt_lock_id lock_id=__acrt_exit_lock, common_exit::__l2::void <lambda>(void) && action=void <lambda>(void){...}) Line 974	C++  
 	throw_exception_spec.exe!common_exit(const int return_code=0, const _crt_exit_cleanup_mode cleanup_mode=_crt_exit_full_cleanup, const _crt_exit_return_mode return_mode=_crt_exit_terminate_process) Line 259	C++  
 	throw_exception_spec.exe!exit(int return_code=0) Line 294	C++  
 	throw_exception_spec.exe!__scrt_common_main_seh() Line 297	C++  
 	throw_exception_spec.exe!__scrt_common_main() Line 331	C++  
 	throw_exception_spec.exe!mainCRTStartup(void * __formal=0x000007fffffd4000) Line 17	C++  
 	kernel32.dll!BaseThreadInitThunk()	Unknown  
 	ntdll.dll!RtlUserThreadStart()	Unknown

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-02-19 08:37:12 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/136#issuecomment-1951947407  

@zlojvavan there's some workaround in https://github.com/boostorg/stacktrace/issues/148. The issues seem to be about the same OS limitation  
  
Does the workaround help?

---

## Comment 2

> Username: zlojvavan  
> Created at: 2024-02-20 08:31:26 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/136#issuecomment-1953706194  

@apolukhin not sure what is workaround there. I suppose it's just the order of static init/finit and there's not much end user can do cleanly if stacktrace lib internals finalized before his own code without author's intervening ;) sometimes I am forced to do such dirty tricks by replacing some static class instances with dynamically created ones and introducing memleak but it's better than crash during finit in my book

---

## Comment 3

> Username: apolukhin  
> Created at: 2024-06-10 19:22:08 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/136#issuecomment-2159124570  

Could you provide a minimal reproducer?  
  
Also note that implementation of the library changed in latest release. Does the issue still happen on new version of the library?

---

## Comment 4

> Username: apolukhin  
> Created at: 2024-07-16 05:47:23 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/136#issuecomment-2230074148  

Updated the library in 1.85 to use the same aproach as the C++ standard library implementation. If the crash remains - it's a platform limitation
