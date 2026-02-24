# #9 Update dll.hpp with WinAPI declarations required by Boost.DLL library [Closed]

> Username: apolukhin  
> Created at: 2015-10-18 07:27:47 UTC  
> Updated at: 2015-10-31 07:27:26 UTC  
> Closed at: 2015-10-28 19:21:31 UTC  
> Url: https://github.com/boostorg/winapi/pull/9  

The file is copied from [Boost.DLL/include/boost/detail/winapi/dll2.hpp](https://github.com/apolukhin/Boost.DLL/blob/master/include/boost/detail/winapi/dll2.hpp)

---

## Comment 1

> Username: Lastique  
> Created_at: 2015-10-18 08:50:37 UTC  
> Updated_at: 2015-10-25 19:15:45 UTC  
> Url: https://github.com/boostorg/winapi/pull/9#discussion_r42318670  

Please, don't remove or replace copyright lines but only add new ones. Even if the year is the same.

---

## Comment 2

> Username: Lastique  
> Created_at: 2015-10-18 08:53:41 UTC  
> Updated_at: 2015-10-25 19:15:45 UTC  
> Url: https://github.com/boostorg/winapi/pull/9#discussion_r42318691  

Please, don't change the coding style of the existing code. I'd rather prefer the new code to follow the existing style.

---

## Comment 3

> Username: Lastique  
> Created_at: 2015-10-18 08:56:56 UTC  
> Updated_at: 2015-10-25 19:15:45 UTC  
> Url: https://github.com/boostorg/winapi/pull/9#discussion_r42318724  

If these constants are only supported from Windows 8, their definition should probably be conditioned on `BOOST_USE_WINAPI_VERSION`, shouldn't it?

---

## Comment 4

> Username: Lastique  
> Created_at: 2015-10-18 09:08:50 UTC  
> Updated_at: 2015-10-25 19:15:45 UTC  
> Url: https://github.com/boostorg/winapi/pull/9#discussion_r42318805  

This function is most probably declared incorrectly (I haven't compared it to the actual declaration in Windows SDK, but fairly sure still). The function must use the same types in the signature as it does in Windows SDK, and it surely doesn't reference `boost::detail::winapi::MEMORY_BASIC_INFORMATION32_` or `boost::detail::winapi::MEMORY_BASIC_INFORMATION64_` there. This header should forward-declare the structure/union/whatever `MEMORY_BASIC_INFORMATION` exactly as it is presented in Windows SDK and use a pointer to it in the `VirtualQuery` forward declaration. `boost::detail::winapi::VirtualQuery` should be an inline function that does a `reinterpret_cast` of the `boost::detail::winapi::MEMORY_BASIC_INFORMATION_` pointer to the pointer to the real `MEMORY_BASIC_INFORMATION`.

---

## Comment 5

> Username: Lastique  
> Created_at: 2015-10-18 09:10:32 UTC  
> Updated_at: 2015-10-25 19:15:45 UTC  
> Url: https://github.com/boostorg/winapi/pull/9#discussion_r42318817  

Does Windows SDK define both these structures? Shouldn't there be just one structure defined differently for different bitness?

---

## Comment 6

> Username: apolukhin  
> Created_at: 2015-10-25 19:21:03 UTC  
> Url: https://github.com/boostorg/winapi/pull/9#issuecomment-150960286  

> If these constants are only supported from Windows 8, their definition should probably be conditioned on BOOST_USE_WINAPI_VERSION, shouldn't it?  
  
This is a tricky question. They are ready out-of-the-box in Windows8, but previous versions of Windows with KB2533623 update also could use it. There's no right way to check those flags at compile time and MSDN recommends some runtime tricks to ensure that those falgs do work.  
  
I've just made sure that they are not used in Boost.DLL any more and removed them...  
  
> Does Windows SDK define both these structures? Shouldn't there be just one structure defined differently for different bitness?  
  
Actually Windows SDK defines 3 different structures: `_MEMORY_BASIC_INFORMATION`, `_MEMORY_BASIC_INFORMATION32`, `_MEMORY_BASIC_INFORMATION64`. `VirtualQuery` always uses `_MEMORY_BASIC_INFORMATION` whose representation depends on bitness.  
  
Left the `boost::detail::winapi::MEMORY_BASIC_INFORMATION_` structure only.  
  
All the other issues were fixed according to your requirements.

---

## Comment 7

> Username: Lastique  
> Created_at: 2015-10-28 19:21:31 UTC  
> Url: https://github.com/boostorg/winapi/pull/9#issuecomment-151962782  

Committed a slightly corrected version of this PR as https://github.com/boostorg/winapi/commit/9ecf1c1b1705e16b64689e85ccca59b5b1dda610.

---

## Comment 8

> Username: apolukhin  
> Created_at: 2015-10-31 07:27:26 UTC  
> Url: https://github.com/boostorg/winapi/pull/9#issuecomment-152707329  

Thanks, works well.

---
