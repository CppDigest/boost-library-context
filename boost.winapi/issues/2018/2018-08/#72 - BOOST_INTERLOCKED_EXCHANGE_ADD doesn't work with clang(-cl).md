# #72 - BOOST_INTERLOCKED_EXCHANGE_ADD doesn't work with clang(-cl) [Closed]

> Username: JVApen  
> Created at: 2018-08-10 20:09:15 UTC  
> Updated at: 2018-08-13 15:57:18 UTC  
> Closed at: 2018-08-13 15:57:18 UTC  
> Url: https://github.com/boostorg/winapi/issues/72  

BOOST_INTERLOCKED_EXCHANGE_ADD and other functions compile making use of clang-cl, however, when used, these cause linker errors because the MinGW functions are used instead of the intrinsic.  
Below, you can find the production.  
  
run.bat  
----------  
cl.exe /IC: \Boost_167_x64 /DBOOST_ALL_NO_LIB /nologo /c /GR /EHsc /fp:precise /FS /std:c++17 /diagnostics:caret /O2 /I. /MDd /Zc:forScope /bigobj /Zc:wchar_t /Zc:inline m.cpp  
move m.obj m.msvc.obj  
link m.msvc.obj /LIBPATH:C: \Boost_167_x64\stage\release\lib /LIBPATH:C:\ Boost_167_x64\stage\debug\lib  
  
c: \llvm_6_0_0\bin\clang-cl.exe -fms-compatibility-version=19.11 -w -Wno-unused-command-line-argument /IC: \Boost_167_x64 /DBOOST_ALL_NO_LIB /nologo /c /GR /EHsc /fp:precise /FS /std:c++17 /diagnostics:caret /O2 /I. /MDd /Zc:forScope /bigobj /Zc:wchar_t m.cpp  
move m.obj m.clang.obj  
link m.clang.obj /LIBPATH:C: \Boost_167_x64\stage\release\lib /LIBPATH:C: \Boost_167_x64\stage\debug\lib  
  
m.cpp  
------  
// Reduced from barrier.hpp  
#include <boost/detail/interlocked.hpp>  
  
int main(int, char**)  
{   
   long active_count = 0, lock_flag_value = 1;  
  long newValue = BOOST_INTERLOCKED_EXCHANGE_ADD(&active_count, lock_flag_value);  
   return 0;  
}  
  
Extra info:  
--------------  
Looks like MSVC gives true for #if defined( BOOST_MSVC ) && BOOST_MSVC >= 1400 (line 80) while Clang ends up with #elif defined( WIN32 ) || defined( _WIN32 ) || defined( __WIN32__ ) || defined( __CYGWIN__ ) (line 154)  
  
By result, <intrin.h> does not get included, which causes a link error:  
unresolved external symbol __imp_InterlockedExchangeAdd referenced in function main  
Defining BOOST_USE_INTRIN_H can be used as workaround.

---

## Comment 1

> Username: Lastique  
> Created at: 2018-08-11 19:50:24 UTC  
> Url: https://github.com/boostorg/winapi/issues/72#issuecomment-412297785  

I don't have clang to test. @pdimov, would you like to take a look?

---

## Comment 2

> Username: pdimov  
> Created at: 2018-08-11 20:58:13 UTC  
> Url: https://github.com/boostorg/winapi/issues/72#issuecomment-412301166  

The smart_ptr-specific version of `interlocked.hpp` is at https://github.com/boostorg/smart_ptr/blob/develop/include/boost/smart_ptr/detail/sp_interlocked.hpp.  
  
I don't remember all the intricacies of this - it's a pain of getting it to work on all the various configurations, each with its own quirks - so the best course of action is probably to just copy it over (removing the `SP_` accordingly.)  
  
Regarding use of `intrin.h` specifically, I see that the `sp` version _would_ choose to use it when `BOOST_MSVC >= 1600`, which would be the case under Clang.

---

## Comment 3

> Username: Lastique  
> Created at: 2018-08-11 21:15:57 UTC  
> Url: https://github.com/boostorg/winapi/issues/72#issuecomment-412302064  

`BOOST_MSVC` is supposed to be only defined for genuine MSVC. It should not be defined for clang, should it?

---

## Comment 4

> Username: pdimov  
> Created at: 2018-08-11 21:21:19 UTC  
> Url: https://github.com/boostorg/winapi/issues/72#issuecomment-412302303  

You're right, it shouldn't be. Hm.

---

## Comment 5

> Username: pdimov  
> Created at: 2018-08-11 22:30:53 UTC  
> Url: https://github.com/boostorg/winapi/issues/72#issuecomment-412305581  

I confirm that the problem occurs with `sp_interlocked.hpp` as well. Should be fixed in https://github.com/boostorg/smart_ptr/commit/4eb53db537b5b5c575dc5524f2d443d82e71bd39. I still couldn't figure out how to get `clang-cl` to work under `b2` though.

---

## Comment 6

> Username: Lastique  
> Created at: 2018-08-13 15:57:18 UTC  
> Url: https://github.com/boostorg/winapi/issues/72#issuecomment-412568612  

Thanks, Peter. I've incorporated some of your changes in bf9dfd9. Hopefully, it fixes the problem.
