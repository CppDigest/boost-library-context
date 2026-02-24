# #159 Implement stacktrace from current exception for MSVC [Merged]

> Username: huangqinjin  
> Created at: 2024-03-16 14:54:10 UTC  
> Updated at: 2024-09-12 14:18:19 UTC  
> Merged at: 2024-04-16 08:39:07 UTC  
> Closed at: 2024-04-16 08:39:07 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/159  

Original stacktrace from `std::exception_ptr` is not possible currently. `std::current_exception()` makes a copy of current exception object into returned `std::exception_ptr`. So the tracking of the original exception object and its stacktrace are lost.  
  
Support for nested exceptions ~is not much meaningful IMO, it~ complicates the implementation (see the last commit). Without nested exceptions support, one can always get stacktrace from current exception immediately after entering `catch` block, **but it loses the original stacktrace for `rethrow_after_other_exception` case**.  
  
The exception object is destroyed by `__DestructExceptionObject` exported from `vcruntime.dll`, there is no simple way to cleanup stored traces in time unless override `__DestructExceptionObject`.

---

## Review 1 [Commented]

> Username: huangqinjin  
> Created_at: 2024-03-16 14:55:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/stacktrace/pull/159#pullrequestreview-1941045511  

📁 build/Jamfile.v2

```diff
 147 |     # Require usable libbacktrace on other platforms
 149 |-     [ check-target-builds ../build//libbacktrace : : <build>no ]
 148 |+     #[ check-target-builds ../build//libbacktrace : : <build>no ]
```

> Username: huangqinjin  
> Created_at: 2024-03-16 14:55:01 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/159#discussion_r1527184719  

I have no idea how to enable this conditionally.

> Username: apolukhin  
> Created_at: 2024-04-12 16:36:35 UTC  
> Updated_at: 2024-04-12 16:39:34 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/159#discussion_r1562818522  

Me too :)  
  
Seems fine for now. If something goes wrong I'll fix it by myself (probably with some help from the b2 developers)


---

## Review 2 [Commented]

> Username: huangqinjin  
> Created_at: 2024-03-16 14:56:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/stacktrace/pull/159#pullrequestreview-1941045679  

📁 test/test_from_exception.cpp

```diff
  33 |   std::string new_msg{msg};
  34 |-   throw std::runtime_error(new_msg);
  34 |+   throw std::logic_error(new_msg);
```

> Username: huangqinjin  
> Created_at: 2024-03-16 14:56:48 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/159#discussion_r1527184888  

Without this change, `in_test_throw_2` and `in_test_throw_1` have identical function body, then seems MSVC merges this two functions, causing stacktrace shows wrong function name.


---

## Review 3 [Changes requested]

> Username: apolukhin  
> Created_at: 2024-04-12 16:39:34 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/stacktrace/pull/159#pullrequestreview-1997887845  

Very impressive!  
  
Added some comments and nitpicks

📁 src/from_exception.cpp

```diff
  39 |+ 
  40 |+ inline bool PER_IS_MSVC_EH(PEXCEPTION_RECORD p) {
  41 |+   DWORD EH_EXCEPTION_NUMBER = 0xE06D7363;
```

> Username: apolukhin  
> Created_at: 2024-04-12 16:30:09 UTC  
> Updated_at: 2024-04-12 16:39:34 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/159#discussion_r1562810220  

constexpr

---

📁 src/from_exception.cpp

```diff
  40 |+ inline bool PER_IS_MSVC_EH(PEXCEPTION_RECORD p) {
  41 |+   DWORD EH_EXCEPTION_NUMBER = 0xE06D7363;
  42 |+   ULONG_PTR EH_MAGIC_NUMBER1 = 0x19930520;
```

> Username: apolukhin  
> Created_at: 2024-04-12 16:30:13 UTC  
> Updated_at: 2024-04-12 16:39:34 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/159#discussion_r1562810315  

constexpr

---

📁 src/from_exception.cpp

```diff
  47 |+ }
  48 |+ 
  49 |+ inline ULONG_PTR PER_PEXCEPTOBJ(PEXCEPTION_RECORD p) {
```

> Username: apolukhin  
> Created_at: 2024-04-12 16:31:01 UTC  
> Updated_at: 2024-04-12 16:39:34 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/159#discussion_r1562811302  

use lowercase to avoid collisions with macro

> Username: huangqinjin  
> Created_at: 2024-04-13 18:39:52 UTC  
> Updated_at: 2024-04-13 18:39:53 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/159#discussion_r1564187870  

It is taken from `ehdata.h` in vcruntime (which is a macro) with slight change. Since we don't include the header directly, I would like to keep as it is. It is the same case as `PER_IS_MSVC_EH`.

---

📁 src/from_exception.cpp

```diff
  38 |+ thread_local exception_data data;
  39 |+ 
  40 |+ inline bool PER_IS_MSVC_EH(PEXCEPTION_RECORD p) {
```

> Username: apolukhin  
> Created_at: 2024-04-12 16:32:23 UTC  
> Updated_at: 2024-04-12 16:39:34 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/159#discussion_r1562813145  

Please add noexcept to all the functions that do not throw


📁 test/test_from_exception.cpp

```diff
 204 |   test_nested();
 205 |   test_rethrow_nested();
 206 |   test_from_other_thread();
```

> Username: apolukhin  
> Created_at: 2024-04-12 16:38:48 UTC  
> Updated_at: 2024-04-12 16:39:34 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/159#discussion_r1562821429  

Also change the   
  
https://github.com/boostorg/stacktrace/blob/351b03d5220510a1b6738cc378659b6d85c8a4da/doc/stacktrace.qbk#L139-L142  
  
to something like  
  
```  
 [warning At the moment the functionality is only available for some of the   
          popular C++ runtimes for POSIX systems with *libbacktrace* or for Windows.   
          Make sure that your platform is supported by running some tests.   
 ]   
```


---

## Review 4 [Commented]

> Username: apolukhin  
> Created_at: 2024-04-12 16:47:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/stacktrace/pull/159#pullrequestreview-1997915896  

📁 src/from_exception.cpp

```diff
  97 |+     if (new_info) {
  98 |+       data.count = new_count;
  99 |+       data.info = (thrown_info*)new_info;
```

> Username: apolukhin  
> Created_at: 2024-04-12 16:47:15 UTC  
> Updated_at: 2024-04-12 16:47:16 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/159#discussion_r1562832319  

use `static_cast<thrown_info*>(new_info)`


---

## Review 5 [Commented]

> Username: apolukhin  
> Created_at: 2024-04-12 16:47:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/stacktrace/pull/159#pullrequestreview-1997916866  

📁 src/from_exception.cpp

```diff
 101 |+       char*& dump_ptr = data.info[data.count - 1].dump;
 102 |+       if (dump_ptr == nullptr) {
 103 |+         dump_ptr = (char*)HeapAlloc(hHeap, 0, kStacktraceDumpSize);
```

> Username: apolukhin  
> Created_at: 2024-04-12 16:47:54 UTC  
> Updated_at: 2024-04-12 16:47:55 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/159#discussion_r1562833191  

use `static_cast<char*>(new_info)` or `reinterpret_cast<char*>(new_info)`


---

## Review 6 [Commented]

> Username: apolukhin  
> Created_at: 2024-04-12 16:51:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/stacktrace/pull/159#pullrequestreview-1997925044  

📁 src/from_exception.cpp

```diff
  11 |+ 
  12 |+ extern "C" void** __cdecl __current_exception(); // exported from vcruntime.dll
  13 |+ #define _pCurrentException static_cast<PEXCEPTION_RECORD>(*__current_exception())
```

> Username: apolukhin  
> Created_at: 2024-04-12 16:51:12 UTC  
> Updated_at: 2024-04-12 16:51:13 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/159#discussion_r1562838385  

make it a function ?

> Username: huangqinjin  
> Created_at: 2024-04-13 18:35:16 UTC  
> Updated_at: 2024-04-13 18:35:17 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/159#discussion_r1564185609  

It is taken from `trnsctrl.h` in vcruntime with slight change. I would like to keep as it is.


---

## Comment 7

> Username: coveralls  
> Created_at: 2024-04-12 16:55:39 UTC  
> Updated_at: 2024-04-15 08:35:07 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/159#issuecomment-2052126431  

[![Coverage Status](https://coveralls.io/builds/66909018/badge)](https://coveralls.io/builds/66909018)  
  
coverage: 86.308%. remained the same  
when pulling **f76e128ab300abdff687590a075aac9e21844247 on huangqinjin:msvc-stacktrace-from-current-exception**  
into **351b03d5220510a1b6738cc378659b6d85c8a4da on boostorg:develop**.

---

## Comment 8

> Username: apolukhin  
> Created_at: 2024-04-16 08:40:17 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/159#issuecomment-2058553630  

Many thanks for the great PR!

---

## Comment 9

> Username: ingo-loehken  
> Created_at: 2024-09-12 14:18:18 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/159#issuecomment-2346433119  

it is possible to track nested exceptions and to map exception_ptr to their callsite, but it can (imho) only be done via IAT Hooking functions in each affected library. Alternatively one may also apply EAT Hooking, if it is guaranteed that the shared libs are not loaded dynamically into foreign processes ... i.e plugin libs into excel or inproc ole/com or other stuff.  
  
Besides that, the perf and abilities of boost::stacktrace are quite impressive. The dep to COM Init should be removed for Windows, if resolving symbols. The implementation of <msvc> seems to bypass the CoInit and therefore avoid apartment model collisions.

---
