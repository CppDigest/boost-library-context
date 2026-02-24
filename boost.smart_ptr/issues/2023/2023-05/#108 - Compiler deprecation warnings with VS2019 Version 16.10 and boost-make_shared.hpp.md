# #108 - Compiler deprecation warnings with VS2019 Version 16.10 and boost/make_shared.hpp [Closed]

> Username: pcaswell  
> Created at: 2023-05-05 08:05:19 UTC  
> Updated at: 2023-05-18 10:21:57 UTC  
> Closed at: 2023-05-18 10:21:56 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/108  

Hello Boost,  
Apologies in advance if this turns out not to be a Boost issue but I'm out of ideas on what to do next.  Here's my setup:  
- Operating System : Windows 10 Enterprise 22H2  
- Compiler : Visual Studio 2019 Version 16.10, Compiler 19.29.30148 for x64  
- Boost : 1.77.0  
  
I've been seeing odd behavior regarding deprecated APIs after including `boost/make_shared.hpp` when the `-external:Wn` and -`external:I<path>` compiler options are used. Here's a simple program to demonstrate:  
  
```  
#include <boost/shared_ptr.hpp>  
#include <boost/make_shared.hpp>  
  
// old way to mark a function as deprecated  
[[deprecated ("Since 1.0.0")]] int DeprecatedA()  
{  
	return 0;  
}  
  
// new way to mark a function as deprecated (since C++14)  
__declspec(deprecated("Since 1.0.1")) int DeprecatedB()  
{  
	return 0;  
}  
  
int main()  
{  
	DeprecatedA();  
	DeprecatedB();  
	return 0;  
}  
  
```  
This code should output two compiler warnings (C4996) when compiled and it does providing I do not compile with these options:  
`-external:W0 -external:I"C:/ThirdParty/v3.0.0/boost-1.77.0/include/boost-1_77" `  
When these options are specified then the above program compiles without warning despite the deprecated functions not being part of boost.  If I comment out the `#include <boost/make_shared.hpp>` line then the compiler outputs deprecation errors.  Also if I do the following then the deprecation errors are output.  
  
```  
#include <boost/shared_ptr.hpp>  
#pragma warning(push)  
#include <boost/make_shared.hpp>  
#pragma warning(pop)  
```  
  
Something isn't right but I'm not at all sure what.  I am suspicious of the VS compiler as it should be outputting warnings but actually outputs this despite the compiler option "Treat warnings as errors" being set to No (/WX-)  
```  
1>good.cpp  
1>C:\Development\main\trials\pcc\deprecated_good\good.cpp(24,2): error C4996: 'DeprecatedA': Since 1.0.0  
1>C:\Development\main\trials\pcc\deprecated_good\good.cpp(25,2): error C4996: 'DeprecatedB': Since 1.0.1  
1>Done building project "deprecated_good.vcxproj" -- FAILED.  
========== Rebuild All: 0 succeeded, 1 failed, 0 skipped ==========  
```  
  
Any help, thoughts and/or suggestions would be appreciated.  
Paul

---

## Comment 1

> Username: pcaswell  
> Created at: 2023-05-05 10:23:57 UTC  
> Updated at: 2023-05-05 11:16:04 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/108#issuecomment-1537023628  

I have just found the problem and it's with the compiler: See:  
https://developercommunity.visualstudio.com/t/external:I-fails-to-restore-warning-sta/1533391?q=external+w0+warning  
Although the comments in the above do seem to imply there's unbalanced pragma push/pops in the boost code.

---

## Comment 2

> Username: pdimov  
> Created at: 2023-05-06 10:04:46 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/108#issuecomment-1537107526  

Unbalanced push/pops sound like a possible reason, but I can see no pragmas in `make_shared.hpp` and the headers included from it (at least those from SmartPtr) that could explain the behavior.  
  
As for 4996 being an error, this is a result of the `/sdl` option.

---

## Comment 3

> Username: pdimov  
> Created at: 2023-05-06 10:15:43 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/108#issuecomment-1537109307  

It's possible that the warning suppression pragmas in `boost/core/allocator_access.hpp` confuse the compiler (https://github.com/boostorg/core/blob/5904fb5636e5c8eb5265a580cd788005a014d750/include/boost/core/allocator_access.hpp#L40-L49 and https://github.com/boostorg/core/blob/5904fb5636e5c8eb5265a580cd788005a014d750/include/boost/core/allocator_access.hpp#L810-L818.)  
  
You may try to apply the fix to https://github.com/boostorg/core/issues/134 and see if it helps.

---

## Comment 4

> Username: pcaswell  
> Created at: 2023-05-11 10:19:54 UTC  
> Updated at: 2023-05-11 10:20:11 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/108#issuecomment-1543730947  

Thanks Peter for the suggestion.  I've tried the fix in https://github.com/boostorg/core/issues/134 with my Boost 1.77 installation and it made no difference.

---

## Comment 5

> Username: pdimov  
> Created at: 2023-05-11 10:26:04 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/108#issuecomment-1543738691  

Does it make a difference if you comment out either the `#ifdef _MSC_VER` portion (https://github.com/boostorg/core/blob/5904fb5636e5c8eb5265a580cd788005a014d750/include/boost/core/allocator_access.hpp#L46-L49 and the corresponding https://github.com/boostorg/core/blob/5904fb5636e5c8eb5265a580cd788005a014d750/include/boost/core/allocator_access.hpp#L810-L812), the `_STL_DISABLE_DEPRECATED_WARNING` portion (https://github.com/boostorg/core/blob/5904fb5636e5c8eb5265a580cd788005a014d750/include/boost/core/allocator_access.hpp#L43C13-L45 and https://github.com/boostorg/core/blob/5904fb5636e5c8eb5265a580cd788005a014d750/include/boost/core/allocator_access.hpp#L813-L815), or both?

---

## Comment 6

> Username: pcaswell  
> Created at: 2023-05-11 10:53:28 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/108#issuecomment-1543773654  

I hope I've understood you properly.  Anyway, with the fix detailed in https://github.com/boostorg/core/issues/134 applied.  
  
Commenting out these lines at the top and bottom of the file had no effect:  
  
```  
//#if defined(_MSC_VER)  
//#pragma warning(push)  
//#pragma warning(disable:4996)  
//#endif  
  
.. boost code ..  
  
//#if defined(_MSC_VER)  
//#pragma warning(pop)  
//#endif  
```  
Likewise commenting out these lines alone had no effect:  
  
```  
//#if defined(_STL_DISABLE_DEPRECATED_WARNING)  
//_STL_DISABLE_DEPRECATED_WARNING  
//#endif  
  
.. boost code ..  
  
//#if defined(_STL_RESTORE_DEPRECATED_WARNING)  
//_STL_RESTORE_DEPRECATED_WARNING  
//#endif  
```  
Commenting out both blocks at the top and bottom of the file has no effect either.  Commenting out all blocks at the top and bottom of the file (_MSC_VER, STL_... and _LIBCPP...) has no effect.  The only thing that makes the compiler spit out the correct warning is to change `-external:I"c:/tools64/v3.0.0/boost-1.77.0/include/boost-1_77"` to `-I"c:/tools64/v3.0.0/boost-1.77.0/include/boost-1_77"`

---

## Comment 7

> Username: pdimov  
> Created at: 2023-05-11 15:21:18 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/108#issuecomment-1544190109  

Then I suppose `boost/core/allocator_access.hpp` is not to blame and the problem comes from somewhere else. I don't know where though. It's not clear how we can diagnose this.

---

## Comment 8

> Username: pdimov  
> Created at: 2023-05-11 15:31:55 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/108#issuecomment-1544207230  

My next guess is that `make_shared.hpp` uses Boost.Move here: https://github.com/boostorg/smart_ptr/blob/bcb2566e744a0467a980c3648d9e92b7c8ccb2bf/include/boost/smart_ptr/make_shared_object.hpp#L16-L17  
  
and Boost.Move has `pragma warning(push)` and `pragma warning(pop)` in separate headers:  
  
https://github.com/boostorg/move/blob/60f782350aa7c64e06ac6d2a6914ff6f6ff35ce1/include/boost/move/detail/config_begin.hpp  
https://github.com/boostorg/move/blob/60f782350aa7c64e06ac6d2a6914ff6f6ff35ce1/include/boost/move/detail/config_end.hpp  
  
You can verify this by either commenting out the part in `config_begin.hpp` that disables 4996, or by surrounding the includes of the Move headers in `make_shared_object.hpp` with `pragma warning(push)` and `pragma warning(pop)`.  
  
(The upside of the second option is that I'll be able to apply it here if it works :-) )

---

## Comment 9

> Username: glenfe  
> Created at: 2023-05-12 09:48:07 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/108#issuecomment-1545478359  

If you replace  
  
```  
#include <boost/make_shared.hpp>  
```  
  
With  
  
```  
#include <boost/smart_ptr/allocate_shared_array.hpp>  
```  
  
Does the problem still occur?

---

## Comment 10

> Username: pcaswell  
> Created at: 2023-05-16 14:38:55 UTC  
> Updated at: 2023-05-16 14:54:31 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/108#issuecomment-1549807736  

Peter, Glen,  
Apologies for the late reply, work was getting in the way.  
  
Commenting out warning 4996 in `boost/move/detail/config_begin.h` fixes the problem.  Also enclosing the move headers in `/boost/smart_ptr/make_shared_object.hpp` (as follows) also fixes the problem.  
  
```  
#pragma warning(push)  
#include <boost/move/core.hpp>  
#include <boost/move/utility_core.hpp>  
#pragma warning(pop)  
```  
Finally, replacing #include `<boost/make_shared.hpp>` with `#include <boost/smart_ptr/allocate_shared_array.hpp>` does not fix the problem.  
Paul

---

## Comment 11

> Username: pcaswell  
> Created at: 2023-05-17 07:41:53 UTC  
> Updated at: 2023-05-17 07:59:02 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/108#issuecomment-1550907617  

>  
> (The upside of the second option is that I'll be able to apply it here if it works :-) )  
>  
If the issue is something to do with the move headers then can it be fixed across the board and not only for the inclusion of `boost/make_shared.hpp` ??  The product I'm working on makes extensive use of boost - I just happened to notice this issue with the inclusion of that header.

---

## Comment 12

> Username: pdimov  
> Created at: 2023-05-17 10:48:00 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/108#issuecomment-1551167174  

I'm not sure how it can be (except on the compiler side.)  
  
But you can open an issue in https://github.com/boostorg/move and ask.

---

## Comment 13

> Username: pcaswell  
> Created at: 2023-05-18 10:21:56 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/108#issuecomment-1552846765  

Thanks for all the help, it seems we can't do any more here so I'll close the issue and ask the question in  https://github.com/boostorg/move
