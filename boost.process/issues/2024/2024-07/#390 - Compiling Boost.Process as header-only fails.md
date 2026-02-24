# #390 - Compiling Boost.Process as header-only fails [Closed]

> Username: DUOLabs333  
> Created at: 2024-07-21 02:04:43 UTC  
> Updated at: 2025-10-01 05:06:02 UTC  
> Closed at: 2024-10-25 01:32:46 UTC  
> Url: https://github.com/boostorg/process/issues/390  

When compiling, I get the error   
```  
&, boost::system::error_code&)':  
/home/system/Repos/tools/external/boost/boost/filesystem/operations.hpp:211:(.text._ZN5boost10filesystem15is_regular_fileERKNS0_4pathERNS_6system10error_codeE[_ZN5boost10filesystem15is_regular_fileERKNS0_4pathERNS_6system10error_codeE]+0x38): undefined reference to `boost::filesystem::detail::status(boost::filesystem::path const&, boost::system::error_code*)'  
/usr/bin/ld: main.o: in function `boost::filesystem::path::append(boost::filesystem::path const&)':  
/home/system/Repos/tools/external/boost/boost/filesystem/path.hpp:1691:(.text._ZN5boost7process6detail5posix11search_pathERKNS_10filesystem4pathERKSt6vectorIS4_SaIS4_EE[_ZN5boost7process6detail5posix11search_pathERKNS_10filesystem4pathERKSt6vectorIS4_SaIS4_EE]+0xcc): undefined reference to `boost::filesystem::detail::path_algorithms::append_v3(boost::filesystem::path&, char const*, char const*)'  
```  
  
This is probably because Process depends on Filesystem, but Filesystem must be separately compiled --- which is something I'm trying to avoid.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-07-22 00:13:37 UTC  
> Url: https://github.com/boostorg/process/issues/390#issuecomment-2241830881  

Only way to get around that is to use C++17 and define `BOOST_PROCESS_USE_STD_FS`.

---

## Comment 2

> Username: DUOLabs333  
> Created at: 2024-07-22 01:00:15 UTC  
> Url: https://github.com/boostorg/process/issues/390#issuecomment-2241859494  

Interestingly, I tried defining it at the top of my main.cpp, but I ran into the same problem.

---

## Comment 3

> Username: jcmonnin  
> Created at: 2024-09-03 08:51:16 UTC  
> Url: https://github.com/boostorg/process/issues/390#issuecomment-2325956182  

Is using Boost.Process v2 as header only still supported?  
  
I have a compile issue under linux using system's boost. It gives:  
```  
undefined reference to `boost::process::v2::environment::detail::is_executable(std::filesystem::__cxx11::path const&, boost::system::error_code&)'  
```  
This was after other link issues before I realized that with 1.86.0 I need to link to `Boost::process` target.  
  
The link issue above seems to come form the system library not defining `BOOST_PROCESS_USE_STD_FS` (the default) but my application does, so now I'm out of luck of using system's boost.  
  
I'm still puzzled as to why this isn't an issue on the windows side. Does it auto-link and defines `BOOST_PROCESS_USE_STD_FS` by default there? Or is it still a header only lib for windows?  
  
It's not the documentation that is going to help. It doesn't mention `BOOST_PROCESS_USE_STD_FS` and also doesn't mention it needs linking. There was just this small mention of `Turned v2 into a compile library.` in the general boost 1.86.0 release notes.  
  
I know this is a bit of a rant and I should be grateful of free libs and maintainer putting effort into it, but I found this transition and the lack of docs rather frustrating.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2024-09-06 15:49:20 UTC  
> Url: https://github.com/boostorg/process/issues/390#issuecomment-2334350790  

Well, that wasn't even close to a rant. I had to do the breaking mode at some point, so I did it while v2 was still experimental as that would cause fewer people do be mad at me.  
  
You'll need to add `boost.process.fs=std` to your b2 invocation, i.e. `b2 boost.process.fs=std`. I think you've built the boost version which would have a different symbol exported there.

---

## Comment 5

> Username: jcmonnin  
> Created at: 2024-09-07 07:13:49 UTC  
> Updated at: 2024-09-07 07:17:58 UTC  
> Url: https://github.com/boostorg/process/issues/390#issuecomment-2335099802  

Thanks for the reply. I used `b2 define=BOOST_PROCESS_USE_STD_FS` before your message. I'm not familiar with b2, i will try `b2 boost.process.fs=std` on next build.  
  
If I understand correctly, v1 is header only, but v2 now has to be compiled and linked.  
  
For binary boost distributions and for linux systems with package managers, it's a bit unfortunate that there is this choice to make  at the time of building boost. Applications using boost make the choice to use either `boost::filesystem` or `std::filesystem` (it's rarely configurable), so you can't have a lib that works for every application of the distribution.  
  
Admittedly this type of issues is common with the C++ ABI model, however most of the times you can say "use the defaults", but here it's a bit more debatable.  
  
Anyway, I was briefly wondering if you could include both overloads in the compiled lib, so that the lib can be used by application using either filesystem implementation, but maybe that comes with it's own issues.

---

## Comment 6

> Username: netfab  
> Created at: 2024-09-14 09:44:36 UTC  
> Updated at: 2024-09-14 11:51:37 UTC  
> Url: https://github.com/boostorg/process/issues/390#issuecomment-2350933083  

> If I understand correctly, v1 is header only, but v2 now has to be compiled and linked.  
>   
In this case maybe this should be updated ?  
https://www.boost.org/doc/libs/1_86_0/more/getting_started/unix-variants.html#header-only-libraries  
  
edit: See also https://github.com/boostorg/more/issues/8

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2024-09-15 23:26:24 UTC  
> Url: https://github.com/boostorg/process/issues/390#issuecomment-2351831247  

I think using both (as default) the in compiled unit might be an option. But it can't be in the public IF since that would be ambiguous. However, I'll have people complaining about the unnecessary dependency on boost.filesystem then, so I can't promise anything yet.

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2024-10-25 01:48:15 UTC  
> Url: https://github.com/boostorg/process/issues/390#issuecomment-2436643337  

I removed filesystem from the compiled libs, i.e. split it up the functions. that means the compiled process library should not care which filesystem library you're using.

---

## Comment 9

> Username: tomaszmi  
> Created at: 2024-10-29 08:01:28 UTC  
> Url: https://github.com/boostorg/process/issues/390#issuecomment-2443503385  

I encountered a similar issue with boost 1.86.0 taken from `vcpkg`. The 1.86.0 has implemented `environment::detail::is_executable` in `./src/detail/environment_posix.cpp` which has got compiled with Boost.filesystem and it forces me now to use Boost.filesystem.   
I see that the recent `develop` branch changed that and `is_executable` is now an inline implemented in `./include/boost/process/v2/detail/environment_posix.hpp` which is a good change I would like to thank you for.

---

## Comment 10

> Username: tomaszmi  
> Created at: 2025-03-24 07:11:44 UTC  
> Url: https://github.com/boostorg/process/issues/390#issuecomment-2747097598  

@klemens-morgenstern when using version 1.87 from vcpkg I see a similar problem, i.e. the compiled process library still depends on a filesystem version selected during the compilation (boost::filesystem in this case), so I cannot yet switch to use std::filesystem. This is a bit unfortunate because it looks like the filesystem is unused there, but symbols have got compiled so I cannot do much.   
  
```  
error_code process_environment::on_setup(windows::default_launcher & launcher, const filesystem::path &, const std::wstring &)  
{  
    if (!unicode_env.empty() && !ec)  
    {  
      launcher.creation_flags |= CREATE_UNICODE_ENVIRONMENT ;  
      launcher.environment = unicode_env.data();  
    }  
  
    return ec;  
};  
  
#else  
  
error_code process_environment::on_setup(posix::default_launcher & launcher, const filesystem::path &, const char * const *)  
{  
    launcher.env = env.data();  
    return error_code{};  
};  
```

---

## Comment 11

> Username: klemens-morgenstern  
> Created at: 2025-03-25 02:54:41 UTC  
> Url: https://github.com/boostorg/process/issues/390#issuecomment-2749922743  

Oh right, I should move those to the header too, then. Hopefully in 1.89.

---

## Comment 12

> Username: kwaegel  
> Created at: 2025-09-30 03:03:46 UTC  
> Url: https://github.com/boostorg/process/issues/390#issuecomment-3349765779  

Was the boost.process header-only bulid fixued in boost 1.89? I'm not seeing it in the current [list of libraries requiring seperate seperate compilation](https://original.boost.org/doc/libs/1_89_0/more/getting_started/windows.html#header-only-libraries), but trying to use the v2 interface seems to cause various link errors.

---

## Comment 13

> Username: klemens-morgenstern  
> Created at: 2025-10-01 01:57:31 UTC  
> Updated at: 2025-10-01 05:06:02 UTC  
> Url: https://github.com/boostorg/process/issues/390#issuecomment-3354400472  

Fixed? No, v2 is not header only anymore.  
  
This list is also helplessly outdated.
