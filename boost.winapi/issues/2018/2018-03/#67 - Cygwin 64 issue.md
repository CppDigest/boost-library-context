# #67 - Cygwin 64 issue [Closed]

> Username: pdimov  
> Created at: 2018-03-08 22:03:43 UTC  
> Updated at: 2018-03-09 13:35:37 UTC  
> Closed at: 2018-03-08 23:53:24 UTC  
> Url: https://github.com/boostorg/winapi/issues/67  

... found by running the System tests:  
  
```  
gcc.compile.c++ ..\..\bin.v2\libs\system\test\error_code_user_test_header.test\g  
cc-6.4.0\debug\address-model-64\target-os-cygwin\error_code_user_test.o  
In file included from /usr/include/w32api/windows.h:70:0,  
                 from test\error_code_user_test.cpp:26:  
/usr/include/w32api/winbase.h:1085:28: error: conflicting declaration of C funct  
ion 'void* LocalAlloc(UINT, SIZE_T)'  
   WINBASEAPI HLOCAL WINAPI LocalAlloc (UINT uFlags, SIZE_T uBytes);  
                            ^~~~~~~~~~  
In file included from ..\../boost/system/detail/local_free_on_destruction.hpp:15  
:0,  
                 from ..\../boost/system/detail/error_code.ipp:28,  
                 from ..\../boost/system/error_code.hpp:766,  
                 from test\error_code_user_test.cpp:17:  
..\../boost/winapi/local_memory.hpp:27:1: note: previous declaration 'void* Loca  
lAlloc(boost::winapi::UINT_, boost::winapi::SIZE_T_)'  
 LocalAlloc(  
 ^~~~~~~~~~  
In file included from /usr/include/w32api/windows.h:70:0,  
                 from test\error_code_user_test.cpp:26:  
/usr/include/w32api/winbase.h:1086:28: error: conflicting declaration of C funct  
ion 'void* LocalReAlloc(HLOCAL, SIZE_T, UINT)'  
   WINBASEAPI HLOCAL WINAPI LocalReAlloc (HLOCAL hMem, SIZE_T uBytes, UINT uFlag  
s);  
                            ^~~~~~~~~~~~  
In file included from ..\../boost/system/detail/local_free_on_destruction.hpp:15  
:0,  
                 from ..\../boost/system/detail/error_code.ipp:28,  
                 from ..\../boost/system/error_code.hpp:766,  
                 from test\error_code_user_test.cpp:17:  
..\../boost/winapi/local_memory.hpp:32:1: note: previous declaration 'void* Loca  
lReAlloc(boost::winapi::HLOCAL_, boost::winapi::SIZE_T_, boost::winapi::UINT_)'  
 LocalReAlloc(  
 ^~~~~~~~~~~~  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2018-03-08 23:53:24 UTC  
> Url: https://github.com/boostorg/winapi/issues/67#issuecomment-371665753  

Thanks, should be fixed after https://github.com/boostorg/winapi/commit/c4ebb6d9d0f274cfbdf50c456a7a17cdc1419d7d.

---

## Comment 2

> Username: pdimov  
> Created at: 2018-03-09 02:46:08 UTC  
> Updated at: 2018-03-09 03:09:55 UTC  
> Url: https://github.com/boostorg/winapi/issues/67#issuecomment-371696101  

Confirmed.

---

## Comment 3

> Username: Lastique  
> Created at: 2018-03-09 08:56:25 UTC  
> Url: https://github.com/boostorg/winapi/issues/67#issuecomment-371752494  

On 03/09/18 05:46, Peter Dimov wrote:  
> Confirmed. I use the |__LP64__| macro to detect Cygwin 64 by the way, if   
> that helps.  
  
`__LP64__` is a general macro that indicates platform with LP64   
convention (i.e. with 64-bit long). It is defined on 64-bit Linux, for   
example.  
  
https://en.wikipedia.org/wiki/64-bit_computing#64-bit_data_models

---

## Comment 4

> Username: pdimov  
> Created at: 2018-03-09 13:35:37 UTC  
> Url: https://github.com/boostorg/winapi/issues/67#issuecomment-371813464  

Yes I know. I also know (now) that you already use `__LP64__`, which is why I edited it out.
