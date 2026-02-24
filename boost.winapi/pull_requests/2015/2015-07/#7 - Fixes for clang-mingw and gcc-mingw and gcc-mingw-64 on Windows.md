# #7 Fixes for clang/mingw and gcc/mingw and gcc/mingw-64 on Windows [Closed]

> Username: eldiener  
> Created at: 2015-07-02 18:07:11 UTC  
> Updated at: 2015-07-12 14:09:05 UTC  
> Closed at: 2015-07-12 12:41:56 UTC  
> Url: https://github.com/boostorg/winapi/pull/7  

Theses fixes makes it possible in winapi to use clang and gcc on Windows with mingw and mingw-64. For mingw-64 only a very few changes had to be made. For mingw ( clang and gcc ) quite a number of changes were made since even the latest mingw w32api package is missing a good deal of Windows functionality. In some cases this meant disabling some winapi functionality for mingw, especially for Vista on up. In other cases, with ConditionVariable, SRWLock, and RunOnce, this meant disabling its functionality entirely for mingw. For these latter three I chose to disable rather than produce #error messages. There is also a bug in the CryptEnumProviders declaration in mingw which makes it unusable and I have reported that to mingw. I decided not to disable that functionality. so that the error is the only one that should occur when running the winapi tests using mingw.

---

## Comment 1

> Username: Lastique  
> Created_at: 2015-07-03 09:38:57 UTC  
> Updated_at: 2015-07-03 09:42:50 UTC  
> Url: https://github.com/boostorg/winapi/pull/7#issuecomment-118298305  

Thanks for the patch. There are a few issues that caught my eye though:  
- IsMingw.hxx should be merged with config.hpp and critical_section_mingw.hxx with critical_section.hpp. BTW, the naming convention in Boost is .hpp not .hxx. Bear in mind that all headers in detail/winapi are internally public and can be included in any order and there should be no headers internal to winapi submodule in there (if such appear in the future, they should go into detail/winapi/detail).  
- If MinGW does not support Vista and later, I believe, we should just define BOOST_USE_WINAPI_VERSION to XP and remove all the extra checks in the code, where possible. If the user defines it to Vista then let it fail to build. Rationale: users of Boost.WinAPI should not be required to bother with BOOST_WINAPI_IS_MINGW and BOOST_WINAPI_IS_MINGW64, a simple BOOST_USE_WINAPI_VERSION check should be enough, if at all needed.  
- I would prefer BOOST_WINAPI_IS_MINGW and BOOST_WINAPI_IS_MINGW64 to be defined when active and not defined otherwise. This is consistent with other feature availability macros in Boost. We also might want to move those to Boost.Predef - it already has BOOST_PLAT_MINGW/BOOST_PLAT_MINGW_AVAILABLE anyway.  
- It's probably better to rename BOOST_WINAPI_IS_MINGW to BOOST_WINAPI_IS_MINGW32 to avoid the confusion.  
- I'm not sure about UnregisterWait and UnregisterWaitEx. Are you sure these are defined in XP+ but RegisterWaitForSingleObject existed earlier? MSDN says all of them are available since 0x0500 (i.e. Win2000), so maybe we should just declare them on MinGW ourselves when missing (linking should succeed anyway).  
- WT_EXECUTEINUITHREAD_ and other constants not present in MinGW should still be defined (manually, with constants). The flags are safe to emulate because it is Windows who implement them, not MinGW.  
- Can you elaborate on the wincrypt.h inclusion and the CryptEnumProviders bug? Can we work around it, perhaps, with inline functions, so that it is transparent to the users of the winapi submodule?

---

## Comment 2

> Username: eldiener  
> Created_at: 2015-07-03 12:51:09 UTC  
> Url: https://github.com/boostorg/winapi/pull/7#issuecomment-118339405  

Let me answer each one of your points. I will number then starting with 1. All references to "mingw" below refers to just mingw while references to "mingw-64" refers to just that.  
  
1) I named them .hxx because your test jamfile was automatically including all .hpp files found in the boost/detail/winapi directory. This was disastrous for a critical_section_mingw.hpp and for IsMingw.hpp I only wanted it included when needed. Once I renamed them to .hxx they were not being included automatically by the test jamfile but only when needed by other .hpp files. I realize that this is only a test problem but I am the one running the tests <g>.   
  
With critical_section_mingw.hxx I have no problem merging it into critical_section.hpp as you requested but I thought it would be easier to follow the code if it was completely separate. With IsMingw.hxx I can merge into config.hpp but then it would be included by all headers even though it is needed by just a few of them. If instead I rename them to .hpp suffix and put them in detail/winapi/detail does your test jamfile still load them automatically ( I don't know whether "[ path.glob-tree $(headers_path) : *.hpp : detail ]" picks up .hpp files in subdirectories of 'detail' or not ) ? If not then that's my preference for IsMingw.hxx at least rather than adding it to config.h. For critical_section_mingw.hxx I will add it directly back into critical_section.hpp rather than including it.  
  
2) I can understand your wanting to simplify mingw to make the code a bit cleaner and clearer, but their are some problems so let me enumerate them:  
  
a) I don't think it is possible to say that MingW doesn't support Vista on up. Their header files for Windows certainly have code for Windows Vista and even Windows 7. They just do a poor job of it.  
  
b) Including boost/config.h when using mingw or mingw-64 already sets _WIN32_WINNT, although windows.h is not included, so forcing BOOST_USE_WINAPI_VERSION to XP for mingw would be error prone.  
  
c) I did not envision BOOST_WINAPI_IS_MINGW or BOOST_WINAPI_IS_MINGW64 as something users of winapi should know about, but rather as macros for internal use only.  
  
3) I don't understand what you mean by defining them when they are "active". I do include the IsMingW header file only when needed. As far as adding them directly to predef and removing them from winapi I am fine with that and agree with you that it is a better place then just in winapi. We need to talk to Rene Rivera and see if he is willing to do that.  
  
4) The rename is fine by me if you really want it. But popularly mingw and mingw-64 are known by those names and references to mingw always refers to just mingw and not both. Furthermore  mingw-64 has both 32 bit and 64 bit distributions. So personally I don't think that using MINGW32 referring to mingw and MINGW64 referring to mingw-64 is the best terminology.  
  
5) It's a mistake in the mingw w32api 4.0+ headers. In the mingw w32api 3.0+ headers it is correct. But mingw stupidly has no way to distinguish between 3.0+ and 4.0+ at compile time so I took the most conservative course for now.  
  
6) OK. Again I was just being conservative because mingw did not have them in their headers.  
  
7) For wincrypt.h in mingw it is just not automatically being included when windows.h is included. The  CryptEnumProviders bug is because of incorrect declarations in the mingw header files. I have already reported the latter to them. The incorrect declarations in their header files are:  
  
```  
WINADVAPI BOOL WINAPI   CryptEnumProvidersA ( DWORD,DWORD*, DWORD,DWORD*, LPTSTR,DWORD*);  
WINADVAPI BOOL WINAPI CryptEnumProvidersW ( DWORD,DWORD*, DWORD,DWORD*, LPTSTR,DWORD*);  
```  
  
instead of the correct:  
  
```  
WINADVAPI BOOL WINAPI CryptEnumProvidersA( DWORD,DWORD*, DWORD,DWORD*, LPSTR,DWORD*);  
WINADVAPI BOOL WINAPI CryptEnumProvidersW( DWORD,DWORD*, DWORD,DWORD*, LPWSTR,DWORD*);  
```

---

## Comment 3

> Username: Lastique  
> Created_at: 2015-07-03 16:09:33 UTC  
> Url: https://github.com/boostorg/winapi/pull/7#issuecomment-118384162  

On 03.07.2015 15:51, Edward Diener wrote:  
  
> Let me answer each one of your points. I will number then starting with  
> 1. All references to "mingw" below refers to just mingw while references  
> to "mingw-64" refers to just that.  
>   
> 1) I named them .hxx because your test jamfile was automatically  
> including all .hpp files found in the boost/detail/winapi directory.  
> This was disastrous for a critical_section_mingw.hpp and for IsMingw.hpp  
  
That's intentional. Any header in boost/detail/winapi can be included at   
any context and as such every header there must be self-sufficient. Both   
critical_section_mingw.hpp and IsMingw.hpp are not self-sufficient and   
that problem is detected by the test.  
  
> With critical_section_mingw.hxx I have no problem merging it into  
> critical_section.hpp as you requested but I thought it would be easier  
> to follow the code if it was completely separate.  
  
I think avoiding code duplication is more important, especially since   
it's difficult to test all possible configurations.  
  
> With IsMingw.hxx I can  
> merge into config.hpp but then it would be included by all headers even  
> though it is needed by just a few of them.  
  
I don't think IsMingw.hxx adds a lot of complexity to config.hpp, so I   
don't think a separate header gains much. If it is _mingw.h what you're   
worried about then it's not a problem - this header is already included   
by Boost.Config.  
  
> If instead I rename them to  
> .hpp suffix and put them in detail/winapi/detail does your test jamfile  
> still load them automatically ( I don't know whether "[ path.glob-tree  
> $(headers_path) : *.hpp : detail ]" picks up .hpp files in  
> subdirectories of 'detail' or not ) ?  
  
The search is recursive but excludes files under detail/winapi/detail   
(the last argument to path.glob-tree rule indicates that).  
  
> 2) I can understand your wanting to simplify mingw to make the code a  
> bit cleaner and clearer, but their are some problems so let me enumerate  
> them:  
>   
> a) I don't think it is possible to say that MingW doesn't support Vista  
> on up. Their header files for Windows certainly have code for Windows  
> Vista and even Windows 7. They just do a poor job of it.  
  
Well, the question is how poor it is. If it doesn't implement SRWLocks,   
condition variables and GetTickCount64 then to us it pretty much   
equivalent to not implemented at all.  
  
So far the only thing Boost libraries had to check for before using a   
particular Windows API is BOOST_USE_WINAPI_VERSION. So if a library   
wants to use e.g. SRWLocks then it tests BOOST_USE_WINAPI_VERSION >=   
BOOST_WINAPI_VERSION_VISTA and goes on. I would very much like to keep   
it this way and not add BOOST_WINAPI_IS_MINGW to the list. This means   
that if we can't emulate MS Windows SDK to the extent of our currently   
supported API subset on MinGW then we should not advertise this   
unsupported version to the users.  
  
I realize that this approach is very conservative but it simplifies the   
library use and makes the code more portable. Perhaps, one day we will   
add separate indication of availability of particular APIs with   
dedicated macros, but so far this is not the case.  
  
> b) Including boost/config.h when using mingw or mingw-64 already sets  
> _WIN32_WINNT, although windows.h is not included, so forcing  
> BOOST_USE_WINAPI_VERSION to XP for mingw would be error prone.  
  
Hmm, that's news to me. Do you know where it gets defined?  
  
> c) I did not envision BOOST_WINAPI_IS_MINGW or BOOST_WINAPI_IS_MINGW64  
> as something users of winapi should know about, but rather as macros for  
> internal use only.  
  
With your current patch they are public in that the user has to check   
them before using certain Windows APIs even if BOOST_USE_WINAPI_VERSION   
indicates that those APIs should be supported.  
  
BTW, private macros should have prefix BOOST_DETAIL_WINAPI_.  
  
> 3) I don't understand what you mean by defining them when they are  
> "active".  
  
I mean BOOST_WINAPI_IS_MINGW should be defined on MinGW and not defined   
on other platforms (i.e. so that the checks are #if   
defined(BOOST_WINAPI_IS_MINGW) as opposed to #if BOOST_WINAPI_IS_MINGW).  
  
> As far  
> as adding them directly to predef and removing them from winapi I am  
> fine with that and agree with you that it is a better place then just in  
> winapi. We need to talk to Rene Rivera and see if he is willing to do that.  
  
Yep. Could you post the proposal on the ML?  
  
> 4) The rename is fine by me if you really want it. But popularly mingw  
> and mingw-64 are known by those names and references to mingw always  
> refers to just mingw and not both. Furthermore mingw-64 has both 32 bit  
> and 64 bit distributions. So personally I don't think that using MINGW32  
> referring to mingw and MINGW64 referring to mingw-64 is the best  
> terminology.  
  
My main motivation is that the legacy MinGW macro is **MINGW32** and   
also there is BOOST_PLAT_MINGW in Boost.Predef which indicates both   
MinGW and MinGW64. We can discuss this on the ML as well as part of the   
proposal to extend Boost.Predef.  
  
> 5) It's a mistake in the mingw w32api 4.0+ headers. In the mingw w32api  
> 3.0+ headers it is correct. But mingw stupidly has no way to distinguish  
> between 3.0+ and 4.0+ at compile time so I took the most conservative  
> course for now.  
  
I think we should declare the function ourselves to hide the MinGW bug.   
winapi public API should be consistent.  
  
> 7) For wincrypt.h in mingw it is just not automatically being included  
> when windows.h is included. The CryptEnumProviders bug is because of  
> incorrect declarations in the mingw header files. I have already  
> reported the latter to them. The incorrect declarations in their header  
> files are:  
>   
> |WINADVAPI BOOL WINAPI   CryptEnumProvidersA ( DWORD,DWORD_, DWORD,DWORD_, LPTSTR,DWORD_);  
> WINADVAPI BOOL WINAPI CryptEnumProvidersW ( DWORD,DWORD_, DWORD,DWORD_, LPTSTR,DWORD_);  
> |  
>   
> instead of the correct:  
>   
> |WINADVAPI BOOL WINAPI CryptEnumProvidersA( DWORD,DWORD_, DWORD,DWORD_, LPSTR,DWORD_);  
> WINADVAPI BOOL WINAPI CryptEnumProvidersW( DWORD,DWORD_, DWORD,DWORD_, LPWSTR,DWORD_);  
> |  
  
Hmm, I'll have to look into this. Unless I'm missing something, with   
these declarations in wincrypt.h would conflict with the ones we have in   
crypt.hpp when BOOST_USE_WINDOWS_H is not defined. Also, the inline   
functions in crypt.hpp would also not compile. I suppose, the inline   
functions could be fixed with something like this:  
  
   class whatever_ptr  
   {  
     void\* m_p;  
   public:  
     explicit whatever_ptr(void\* p) : m_p(p) {}  
     template< typename T >  
     operator T\* () const noexcept { return static_cast< T\* >(m_p); }  
   };  
  
We can then use whatever_ptr to forward pointer arguments that are   
declared differently in different SDKs.

---

## Comment 4

> Username: eldiener  
> Created_at: 2015-07-03 22:57:05 UTC  
> Url: https://github.com/boostorg/winapi/pull/7#issuecomment-118436203  

I remove the separate .hxx files. So let me go on to other issues:  
  
1) The boost/config.h header includes _mingw.h when it determines that the platform is Windows. The _mingw.h include, for whatever reason in both mingw and mingw-64, sets the _WIN32_WINNT value.  
  
2) I don't mind limiting mingw to less than Windows Vista and simplifying the code. The default _WIN32_WINNT for mingw is Windows 2000 while the default _WIN32_WINNT for mingw-64 is Windows XP with SP2. But if this is done you should document it. Mingw-64 of course doesn't need such a limitation. Luckily it appears that the latest update to clang source allows using mingw-64 as a gcc Windows target rather than just mingw. But past clang Windows releases still have the limitation to mingw.  
  
3) I will bring up predef distinguishing between mingw and mingw64 on the developers mailing list. BTW predef is essentially picking up the same macros that my BOOST_WINAPI_IS_MINGW(64) macros are picking up ( it's also including _mingw.h ).  
  
4) While my macro is being used I still think specifying MINGW32 and MINGW64 to distinguish between mingw and mingw-64 is bad naming. Consider that mingw-64 itself has 32bit and 64bit implementations. Doesn't MINGW32 sound like a 32-bit implementation of mingw-64 rather than a referral to mingw ? Furthermore the predefined macro **MINGW32** in both mingw and mingw-64 simply says that either mingw or mingw-64 is the platform. I would like to avoid one more confusing use of the MINGW32 mnemonic.  
  
5) Regarding UnregisterWait and UnregisterWaitEx for mingw, as well as the CryptEnumProviders bug in mingw, I would like for you to provide this workaround once you merge the PR into 'develop'. I will be glad to test it for you then. I am not sure how these workarounds are supposed to be coded and you have already done some of this in winapi and know what you want.  
  
My next update will be to remove the mingw Vista use of BOOST_WINAPI_IS_MINGW and defining the WT_EXECUTEINUITHREAD_ etc., which is not in the mingw headers, with constants.

---

## Comment 5

> Username: Lastique  
> Created_at: 2015-07-04 06:23:49 UTC  
> Url: https://github.com/boostorg/winapi/pull/7#issuecomment-118468186  

On 04.07.2015 01:57, Edward Diener wrote:  
  
> I remove the separate .hxx files. So let me go on to other issues:  
>   
> 1) The boost/config.h header includes _mingw.h when it determines that  
> the platform is Windows. The _mingw.h include, for whatever reason in  
> both mingw and mingw-64, sets the _WIN32_WINNT value.  
  
I see. It is unfortunate that the macro is defined in _mingw.h because   
it influences our default target version and there seems to be no way   
around this.  
  
> 2) I don't mind limiting mingw to less than Windows Vista and  
> simplifying the code. The default _WIN32_WINNT for mingw is Windows 2000  
> while the default _WIN32_WINNT for mingw-64 is Windows XP with SP2. But  
> if this is done you should document it. Mingw-64 of course doesn't need  
> such a limitation.  
  
If I'm not mistaken, the current code in winapi/config.hpp should have   
been defaulting to Windows 2000/XP on MinGW/MinGW64, so nothing really   
changed. I wonder why you needed to add checks on   
BOOST_WINAPI_IS_MINGW(64) in the code.  
  
There is no documentation in the winapi submodule but I could mention   
something about the default target Windows versions in Boost release notes.  
  
> 3) I will bring up predef distinguishing between mingw and mingw64 on  
> the developers mailing list. BTW predef is essentially picking up the  
> same macros that my BOOST_WINAPI_IS_MINGW(64) macros are picking up (  
> it's also including _mingw.h ).  
  
Ok, thanks.  
  
> 4) While my macro is being used I still think specifying MINGW32 and  
> MINGW64 to distinguish between mingw and mingw-64 is bad naming.  
> Consider that mingw-64 itself has 32bit and 64bit implementations.  
> Doesn't MINGW32 sound like a 32-bit implementation of mingw-64 rather  
> than a referral to mingw ? Furthermore the predefined macro _MINGW32_ in  
> both mingw and mingw-64 simply says that either mingw or mingw-64 is the  
> platform. I would like to avoid one more confusing use of the MINGW32  
> mnemonic.  
  
Ok, let's keep it BOOST_WINAPI_IS_MINGW.  
  
> 5) Regarding UnregisterWait and UnregisterWaitEx for mingw, as well as  
> the CryptEnumProviders bug in mingw, I would like for you to provide  
> this workaround once you merge the PR into 'develop'. I will be glad to  
> test it for you then. I am not sure how these workarounds are supposed  
> to be coded and you have already done some of this in winapi and know  
> what you want.  
  
Ok.  
  
> My next update will be to remove the mingw Vista use of  
> BOOST_WINAPI_IS_MINGW and defining the WT_EXECUTEINUITHREAD_ etc., which  
> is not in the mingw headers, with constants.  
  
Ok, thanks for all your work.

---

## Comment 6

> Username: eldiener  
> Created_at: 2015-07-05 22:06:00 UTC  
> Url: https://github.com/boostorg/winapi/pull/7#issuecomment-118670563  

On 7/3/2015 11:23 PM, Andrey Semashev wrote:  
  
> On 04.07.2015 01:57, Edward Diener wrote:  
>   
> > I remove the separate .hxx files. So let me go on to other issues:  
> >   
> > 1) The boost/config.h header includes _mingw.h when it determines that  
> > the platform is Windows. The _mingw.h include, for whatever reason in  
> > both mingw and mingw-64, sets the _WIN32_WINNT value.  
>   
> I see. It is unfortunate that the macro is defined in _mingw.h because  
> it influences our default target version and there seems to be no way  
> around this.  
> Neither you nor I can control what mingw or mingw-64 decide to do. I   
> have had discussions with them on their mailing lists about various   
> issues but getting them to change anything in their win32 api   
> implementations is very difficult to do. They don't see their win32 API   
> implementations as very important compared to their general integration   
> of the gcc compilers itself with Windows.  
>   
> > 2) I don't mind limiting mingw to less than Windows Vista and  
> > simplifying the code. The default _WIN32_WINNT for mingw is Windows 2000  
> > while the default _WIN32_WINNT for mingw-64 is Windows XP with SP2. But  
> > if this is done you should document it. Mingw-64 of course doesn't need  
> > such a limitation.  
>   
> If I'm not mistaken, the current code in winapi/config.hpp should have  
> been defaulting to Windows 2000/XP on MinGW/MinGW64, so nothing really  
> changed. I wonder why you needed to add checks on  
> BOOST_WINAPI_IS_MINGW(64) in the code.  
> Please look at my code after the last update. The number of specific   
> checks for mingw is pretty minimal and there is only a singlwe check for   
> mingw-64. If you merge to develop you are of course free to change what   
> you like. But please consider that my changes are just trying to make it   
> easier for those using gcc and/or clang on Windows to use Boost   
> libraries, some of which use the Boost.winapi functionality.  
  
The good news with clang is that a recent update to the clang source   
makes it possible to use a mingw-64 gcc implementation as the gcc RTL on   
Windows, whereas before only a mingw gcc implementation could be used.   
Unfortunately binary versions of previously releases of clang for   
Windows ( clang-3.41, clang 3.5.2, clang 3.6.1 ) are still restricted to   
using mingw's gcc RTL. Clearly the mingw-64 implementations are better.  
  
> There is no documentation in the winapi submodule but I could mention  
> something about the default target Windows versions in Boost release   
> notes.  
>   
> > 3) I will bring up predef distinguishing between mingw and mingw64 on  
> > the developers mailing list. BTW predef is essentially picking up the  
> > same macros that my BOOST_WINAPI_IS_MINGW(64) macros are picking up (  
> > it's also including _mingw.h ).  
>   
> Ok, thanks.  
> Rene Rivera ( predef ) will be away for at least a month so I will wait   
> to being up this issue for predef, or perhaps just bring it up as a   
> Boost trac suggestion for now.  
>   
> > 4) While my macro is being used I still think specifying MINGW32 and  
> > MINGW64 to distinguish between mingw and mingw-64 is bad naming.  
> > Consider that mingw-64 itself has 32bit and 64bit implementations.  
> > Doesn't MINGW32 sound like a 32-bit implementation of mingw-64 rather  
> > than a referral to mingw ? Furthermore the predefined macro _MINGW32_ in  
> > both mingw and mingw-64 simply says that either mingw or mingw-64 is the  
> > platform. I would like to avoid one more confusing use of the MINGW32  
> > mnemonic.  
>   
> Ok, let's keep it BOOST_WINAPI_IS_MINGW.  
>   
> > 5) Regarding UnregisterWait and UnregisterWaitEx for mingw, as well as  
> > the CryptEnumProviders bug in mingw, I would like for you to provide  
> > this workaround once you merge the PR into 'develop'. I will be glad to  
> > test it for you then. I am not sure how these workarounds are supposed  
> > to be coded and you have already done some of this in winapi and know  
> > what you want.  
>   
> Ok.  
>   
> > My next update will be to remove the mingw Vista use of  
> > BOOST_WINAPI_IS_MINGW and defining the WT_EXECUTEINUITHREAD_ etc., which  
> > is not in the mingw headers, with constants.  
>   
> Ok, thanks for all your work.  
> You are welcome. If/when you merge and make whatever other changes you   
> deem necessary I will be glad to run the winapi tests using the various   
> mingw/gcc, mingw-64/gcc, clang/mingw/gcc, and clang/mingw-64/gcc   
> implementations I have on my machine. Needless to say I can also test   
> VC++8 through VC++12 also if necessary.

---

## Comment 7

> Username: Lastique  
> Created_at: 2015-07-12 12:41:56 UTC  
> Url: https://github.com/boostorg/winapi/pull/7#issuecomment-120718513  

The modified changes were committed as a part of https://github.com/boostorg/winapi/commit/4f7d318b87032a05e5e5510dc931eb38fa7b835a.

---

## Comment 8

> Username: eldiener  
> Created_at: 2015-07-12 14:09:05 UTC  
> Url: https://github.com/boostorg/winapi/pull/7#issuecomment-120722537  

Thanks !

---
