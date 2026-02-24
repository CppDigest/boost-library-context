# #71 boostorg/winapi/issues/70 Support Windows CE 6.00, 7.00 and WM 6.x [Merged]

> Username: xentrax  
> Created at: 2018-06-19 12:27:18 UTC  
> Updated at: 2020-05-01 23:15:27 UTC  
> Merged at: 2020-05-01 23:15:26 UTC  
> Closed at: 2020-05-01 23:15:26 UTC  
> Url: https://github.com/boostorg/winapi/pull/71  

As discussed in https://github.com/boostorg/winapi/issues/70 introduced use of   
BOOST_WINAPI_IMPORT and BOOST_WINAPI_IMPORT_EXCEPT_WM instead of BOOST_SYMBOL_IMPORT.  
  
The macros were declared in config.hpp  
  
Also fixed issues caused by different signatures of some WINAPI calls in   
critical_section.hpp  
process.hpp  
local_memory.hpp

---

## Review 1 [Changes requested]

> Username: Lastique  
> Created_at: 2018-08-08 18:16:21 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/winapi/pull/71#pullrequestreview-144537934  

📁 include/boost/winapi/critical_section.hpp

```diff
  25 |+ #if defined (_WIN32_WCE)
  26 |+ struct CRITICAL_SECTION;
  27 |+ typedef CRITICAL_SECTION BOOST_WINAPI_RTL_CRITICAL_SECTION;
```

> Username: Lastique  
> Created_at: 2018-08-08 17:58:42 UTC  
> Updated_at: 2018-10-23 12:48:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#discussion_r208678164  

No global typedefs, please. Define a lowercase typedef in `boost::winapi::detail` and use it in the declarations.

> Username: xentrax  
> Created_at: 2018-10-19 21:45:57 UTC  
> Updated_at: 2018-10-23 12:48:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#discussion_r226790065  

This suggestion, if implemented literally, causes 3 issues.  
1.  public boost::winapi interface mentions detail namespace  
2.  it create illegal overload with extern "C" linkage  
3. it causes ambiguity if unqualified call (like error C2668: 'EnterCriticalSection': ambiguous call to overloaded function)  
  
I can amend the commit to exclude the change about typedef for _RTL_CRITICAL_SECTION altogether, and open separate PR only about that. What do you think?

> Username: Lastique  
> Created_at: 2018-10-19 23:33:03 UTC  
> Updated_at: 2018-10-23 12:48:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#discussion_r226804744  

1. Not sure I understand what you mean. Public declarations in `boost::winapi` do not and should not use neither `::_RTL_CRITICAL_SECTION` nor `::CRITICAL_SECTION` but a dedicated structure `boost::winapi::_RTL_CRITICAL_SECTION`. The typedef I suggested (let's call it `boost::winapi::detail::winsdk_critical_section`) will only be used in the declarations in the global namespace - i.e. those mirroring the ones from Windows SDK.  
2. Since the typedef will refer to the same structure that is defined in the Windows SDK, this will not be an overload but rather a declaration of the same function.  
3. Unqualified calls from within Boost should not happen since all Boost.WinAPI public symbols are in `boost::winapi`. Any Boost library that is using Boost.WinAPI has to specify at the very least `winapi` namespace. Unqualified calls from non-Boost user's code will resolve to the function from the Windows SDK (see point 2; the declaration in Boost.WinAPI must be equivalent to that in Windows SDK and thus not introduce an overload).

> Username: xentrax  
> Created_at: 2018-10-20 07:47:41 UTC  
> Updated_at: 2018-10-23 12:48:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#discussion_r226819614  

ok, it works, sorry.  
  
3 more questions.  
  
1.  
Do you prefer boost::winapi::detail::winapi_critical_section  
or boost::winapi::detail::winsdk_critical_section ?  
  
2.   
I can apply same approach for MINGW #ifdef in the same file for consistency.  
  
3.  
The internal layout of the CRITICAL_SECTION  on CE is different from NT. Fortunately, sizeof of CE variant is smaller.  
  
boost::winapi::_RTL_CRITICAL_SECTION mimics NT. Do we want to do something about it?  
  
This is CE variant:  
```  
typedef struct CRITICAL_SECTION {  
    unsigned int LockCount;         /* Nesting count on critical section */  
    HANDLE OwnerThread;         	/* Handle of owner thread */  
    HANDLE hCrit;					/* Handle to this critical section */  
    DWORD needtrap;					/* Trap in when freeing critical section */  
    DWORD dwContentions;			/* Count of contentions */  
} CRITICAL_SECTION, *LPCRITICAL_SECTION;  
```

> Username: Lastique  
> Created_at: 2018-10-20 09:32:19 UTC  
> Updated_at: 2018-10-23 12:48:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#discussion_r226821911  

1. `winsdk_critical_section` is better as it implies that it refers to the type defined in Windows SDK rather than Boost.WinAPI.  
2. Yes, that would be great, thanks. Do check that other than the structure name the function signatures are the same, though.  
3. I think it would be better to mimic the CE structure on Windows CE.


📁 include/boost/winapi/process.hpp

```diff
 459 |+     LPWSTR_ lpCurrentDirectory,
 460 |+ #else
 461 |+ 	LPCWSTR_ lpCurrentDirectory,
```

> Username: Lastique  
> Created_at: 2018-08-08 18:03:00 UTC  
> Updated_at: 2018-10-23 12:48:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#discussion_r208679606  

No tabs, please. Here and everywhere else.

> Username: xentrax  
> Created_at: 2018-10-19 21:46:10 UTC  
> Updated_at: 2018-10-23 12:48:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#discussion_r226790105  

agree

---

📁 include/boost/winapi/process.hpp

```diff
  50 |- BOOST_SYMBOL_IMPORT BOOST_NORETURN boost::winapi::VOID_ BOOST_WINAPI_WINAPI_CC
  50 |+ #if !defined(_WIN32_WCE)
  51 |+ // On Windows CE ExitProcess is a macro to call TerminateProcess
```

> Username: Lastique  
> Created_at: 2018-08-08 18:05:10 UTC  
> Updated_at: 2018-10-23 12:48:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#discussion_r208680283  

In this case, Boost.WinAPI has to provide a portable way to call `ExitProcess`, including in cases when it is a macro and when it is not defined. The best approach is to provide an inline wrapper function in `boost::winapi` (e.g. `exit_process`).

> Username: xentrax  
> Created_at: 2018-10-19 21:46:33 UTC  
> Updated_at: 2018-10-23 12:48:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#discussion_r226790184  

agree

---

📁 include/boost/winapi/process.hpp

```diff
  81 |+ 	boost::winapi::DWORD_ dwCreationFlags,
  82 |+ 	boost::winapi::LPVOID_ lpEnvironment,
  83 |+ 	boost::winapi::LPWSTR_ lpCurrentDirectory,
```

> Username: Lastique  
> Created_at: 2018-08-08 18:08:21 UTC  
> Updated_at: 2018-10-23 12:48:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#discussion_r208681318  

Here and in other functions, changing `LPCWSTR_` to `LPWSTR_` is not portable as it breaks user's code which passes constant strings as arguments. If this function actually does not modify the argument, there needs to be an inline wrapper function in `boost::winapi` which will cast away the constness.

> Username: xentrax  
> Created_at: 2018-10-19 21:46:19 UTC  
> Updated_at: 2018-10-23 12:48:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#discussion_r226790135  

agree


---

## Comment 2

> Username: xentrax  
> Created_at: 2018-10-21 21:07:07 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#issuecomment-431703996  

The changes discussed so far are implemented in 3 commits because the 17fbd4a and 10c4126 are   
somewhat independent and, also, not critical for the original problem.  
  
Is the anything else I can do here?

---

## Review 3 [Changes requested]

> Username: Lastique  
> Created_at: 2018-10-22 18:35:08 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/winapi/pull/71#pullrequestreview-167046619  

📁 include/boost/winapi/process.hpp

```diff
 398 |+     boost::winapi::UINT_ uExitCode)
 399 |+ {
 400 |+     ExitProcess(uExitCode);
```

> Username: Lastique  
> Created_at: 2018-10-22 18:31:13 UTC  
> Updated_at: 2018-10-23 12:48:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#discussion_r227085590  

Where is this macro defined on CE? You haven't added any #includes to this header.

> Username: xentrax  
> Created_at: 2018-10-23 06:52:04 UTC  
> Updated_at: 2018-10-23 12:48:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#discussion_r227246392  

Good catch, thanks. To support compilation without including any headers I will call TerminateProcess directly instead of "calling" ExitProcess macro.

> Username: xentrax  
> Created_at: 2018-10-23 07:24:56 UTC  
> Updated_at: 2018-10-23 12:48:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#discussion_r227255829  

Replicating the macro will lead to this code. I think it's ok.  
  
```  
BOOST_FORCEINLINE BOOST_NORETURN boost::winapi::VOID_ exit_process(  
    boost::winapi::UINT_ uExitCode)  
{  
#if !defined(_WIN32_WCE)  
    ExitProcess(uExitCode);  
#else  
    // ExitProcess macro in Windows CE and Windows Mobile SDKs actually does this  
    TerminateProcess(GetCurrentProcess(), (uExitCode));  
#endif  
}  
  
```

> Username: Lastique  
> Created_at: 2018-10-23 08:02:04 UTC  
> Updated_at: 2018-10-23 12:48:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#discussion_r227269170  

Yes, though no need for the additional parenthesis around `uExitCode` since we're not in a macro.


📁 include/boost/winapi/critical_section.hpp

```diff
  28 |+ namespace winapi {
  29 |+ namespace detail {
  30 |+     typedef CRITICAL_SECTION winapi_critical_section;
```

> Username: Lastique  
> Created_at: 2018-10-22 18:33:05 UTC  
> Updated_at: 2018-10-23 12:48:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#discussion_r227086331  

I thought we settled on `winsdk_critical_section`, didn't we?

---

📁 include/boost/winapi/critical_section.hpp

```diff
 163 |+ #else
 164 |+ 
 165 |+ typedef struct BOOST_MAY_ALIAS CRITICAL_SECTION {
```

> Username: Lastique  
> Created_at: 2018-10-22 18:34:32 UTC  
> Updated_at: 2018-10-23 12:48:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#discussion_r227086860  

Better use the same name Windows SDK uses for the structure, i.e. `_CRITICAL_SECTION`.

> Username: xentrax  
> Created_at: 2018-10-23 07:00:15 UTC  
> Updated_at: 2018-10-23 12:48:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#discussion_r227248629  

Yes, big Windows SDK uses "_RTL_CRITICAL_SECTION" name in its SDK.  
Windows CE SDKs uses CRITICAL_SECTION name.  
  
I don't see any harm in using _RTL_CRITICAL_SECTION here, but IMHO it seems inconsistent with your previous strict approach, and may confuse other developers targeting Windows CE.  
  
Of course, I will use _RTL_CRITICAL_SECTION. But if you have a second thought, no problem, just tell.


---

## Comment 4

> Username: Lastique  
> Created_at: 2018-10-22 18:36:39 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#issuecomment-431929091  

Also, your PR seems to include some changes that are in develop. Could you rebase the PR against the current develop? It would make reviewing a little easier.

---

## Comment 5

> Username: Lastique  
> Created_at: 2018-10-23 08:06:09 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#issuecomment-432140528  

> Yes, big Windows SDK uses "_RTL_CRITICAL_SECTION" name in its SDK.  
> Windows CE SDKs uses CRITICAL_SECTION name.  
  
Oh, I misread the code then (for some reason I thought CRITICAL_SECTION   
was a typedef to _CRITICAL_SECTION). Sure, let's leave CRITICAL_SECTION   
then.

---

## Comment 6

> Username: xentrax  
> Created_at: 2018-10-23 19:46:09 UTC  
> Url: https://github.com/boostorg/winapi/pull/71#issuecomment-432392216  

I fixed the updated list of problems, I tried hard to not forget about winsdk_critical_section and tabs this time.  
  
I reinstated CRITICAL_SECTION name to match Windows CE SDKs.  
   
Inspired by the ExitProcess remark I also fixed a small compilation issue for the case when boost/winapi headers are included before anything else. (A couple of typedefs added in basic_types.hpp).

---
