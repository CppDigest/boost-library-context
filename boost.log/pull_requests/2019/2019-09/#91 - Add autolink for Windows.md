# #91 Add autolink for Windows [Closed]

> Username: diorcety  
> Created at: 2019-09-26 08:24:07 UTC  
> Updated at: 2019-09-26 12:13:40 UTC  
> Closed at: 2019-09-26 08:40:05 UTC  
> Url: https://github.com/boostorg/log/pull/91  

I'm using clang on windows using clang-win toolset.  
I have issue when linking the log library:  
  
```  
event_log_backend.obj : error LNK2019: unresolved external symbol EnumProcessModules referenced in function "void __cdecl boost::log::v2_mt_nt5::sinks::`anonymous namespace'::init_self_module_handle(struct HINSTANCE__ * &)" (?init_self_module_handle@?A@sinks@v2_mt_nt5@log@boost@@YAXAEAPEAUHINSTANCE__@@@Z)  
  
event_log_backend.obj : error LNK2019: unresolved external symbol GetModuleInformation referenced in function "void __cdecl boost::log::v2_mt_nt5::sinks::`anonymous namespace'::init_self_module_handle(struct HINSTANCE__ * &)" (?init_self_module_handle@?A@sinks@v2_mt_nt5@log@boost@@YAXAEAPEAUHINSTANCE__@@@Z)  
  
object_name.obj : error LNK2019: unresolved external symbol GetUserNameExW referenced in function "class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > __cdecl boost::log::v2_mt_nt5::ipc::`anonymous namespace'::get_scope_prefix(enum log::v2_mt_nt5::ipc::A::object_name::scope)" (?get_scope_prefix@?A@ipc@v2_mt_nt5@log@boost@@YA?AV?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@@std@@W4scope@object_name@1234@@Z)  
```  
  
I don't know if this is the good way to do that but I solved the issue with this patch

---

## Comment 1

> Username: Lastique  
> Created_at: 2019-09-26 08:40:05 UTC  
> Url: https://github.com/boostorg/log/pull/91#issuecomment-535402932  

There is no need to add autolinking in the source code, the libraries are linked using the build system (Boost.Build).

---

## Comment 2

> Username: diorcety  
> Created_at: 2019-09-26 08:52:52 UTC  
> Url: https://github.com/boostorg/log/pull/91#issuecomment-535407639  

That doesn't explain why I have the missing symbols

---

## Comment 3

> Username: Lastique  
> Created_at: 2019-09-26 09:11:32 UTC  
> Url: https://github.com/boostorg/log/pull/91#issuecomment-535414676  

If you're seeing these errors while building Boost following the [official guideline](https://www.boost.org/doc/libs/1_71_0/more/getting_started/windows.html#or-build-binaries-from-source) or similarly (i.e. by invoking `b2`) then the problem is either in Boost.Build (if it doesn't support your toolset) or in clang (if it doesn't link the libraries specified in the command line). You can verify that the libraries are added in the compiler command line by adding `-d+2` to `b2`command line.  
  
If you're building Boost in some other way then it is your responsibility to reproduce the build logic specified in Boost.Build scripts for the library. Sorry, I cannot help you here except to point you to the build scripts: https://github.com/boostorg/log/tree/develop/build.  
  
If you're seeing these errors while building your application then there is some configuration issue on your end because, as indicated by `v2_mt_nt5`, Boost.Log is built as a shared library, but in that case the libraries it requires should have been linked when that shared library was built. It seems, you're linking object files of the library into your application, and that is not the correct way to use the library. You should be linking with the shared library instead.

---

## Comment 4

> Username: diorcety  
> Created_at: 2019-09-26 09:35:00 UTC  
> Updated_at: 2019-09-26 09:35:19 UTC  
> Url: https://github.com/boostorg/log/pull/91#issuecomment-535423505  

I will look on the build side. But the missing symbols is when building boost log, not when using it.

---

## Comment 5

> Username: diorcety  
> Created_at: 2019-09-26 12:13:39 UTC  
> Url: https://github.com/boostorg/log/pull/91#issuecomment-535474814  

Please check https://ci.appveyor.com/project/Lastique/log/builds/27685606 from #92

---
