# #70 - Windows CE 7.0 and Windows Mobile 6.0 do not always dllimport WINAPI functions [Closed]

> Username: xentrax  
> Created at: 2018-06-16 22:20:57 UTC  
> Updated at: 2020-05-01 23:17:05 UTC  
> Closed at: 2020-05-01 23:17:05 UTC  
> Url: https://github.com/boostorg/winapi/issues/70  

Use of BOOST_SYMBOL_IMPORT is not a good choice.  
  
Something like BOOST_WINAPI_SYMBOL_IMPORT is needed, so that it could be defined to nothing for #ifdef _WIN32_WCE  
  
It could be an easy solution but...  
  
But there is a complication for Windows Mobile 6.0, (SDK is included into Visual Studio 2008 Prof).  
Half of the WINAPI functions are __declspec(dllimport), and another half are linked locally with corelib.dll  
  
So, ideally all WINAPI mapping in boost::winapi should have either BOOST_WINAPI_SYMBOL_IMPORT1 or BOOST_WINAPI_SYMBOL_IMPORT2.  
  
Hence for defined(_WIN32_WCE) &&  defined(WIN32_PLATFORM_PSPC)  only one of those will be defined to nothing, and another half will be defined to BOOST_SYMBOL_IMPORT normally.  
  
My preliminary experiments showed that the mentioned changes and and a couple #ifdefs for missing Ex WINAPIs are enough to make this WM and CE-related regression in boost 1.67 go away. Deciding BOOST_WINAPI_SYMBOL_IMPORT1  vs BOOST_WINAPI_SYMBOL_IMPORT2 manually may take a few hours of manual work.

---

## Comment 1

> Username: Lastique  
> Created at: 2018-06-17 19:12:09 UTC  
> Url: https://github.com/boostorg/winapi/issues/70#issuecomment-397899824  

As I said on the mailing list, we don't have a Windows Mobile tester, so this platform is essentially unsupported. Unless there is a volunteer to do the porting, things are unlikely to change in the forseeable future. I can consider a PR, although it will have to be rather well documented and, naturally, not break the mainstream platforms. In particular, macro names like `BOOST_WINAPI_SYMBOL_IMPORT1` and `BOOST_WINAPI_SYMBOL_IMPORT2` are not descriptive enough.

---

## Comment 2

> Username: xentrax  
> Created at: 2018-06-17 20:55:29 UTC  
> Url: https://github.com/boostorg/winapi/issues/70#issuecomment-397905966  

I am struggling with good naming too.   
  
What about  
BOOST_WINAPI_IMPORT_PC_AND_PSPC  
and  
BOOST_WINAPI_IMPORT_PC  
?  
  
On CE both will be defined to nothing. When compiling with WM SDK only the second will be defined to nothing.

---

## Comment 3

> Username: Lastique  
> Created at: 2018-06-17 22:27:44 UTC  
> Url: https://github.com/boostorg/winapi/issues/70#issuecomment-397910752  

What does PSPC stand for?

---

## Comment 4

> Username: xentrax  
> Created at: 2018-06-18 04:12:25 UTC  
> Url: https://github.com/boostorg/winapi/issues/70#issuecomment-397937581  

WIN32_PLATFORM_PSPC is an ancient macro to detect so called "**P**alm **S**ize PC" platform.  
It was rebranded to Windows Mobile in 2003.  
https://en.wikipedia.org/wiki/Windows_Mobile#Pocket_PC_2000  
  
But "WM" suffix can be more confusing for modern developers who would think it is about Win RT phones (2013). https://en.wikipedia.org/wiki/Windows_10_Mobile  
But it not related at all.

---

## Comment 5

> Username: Lastique  
> Created at: 2018-06-18 13:14:43 UTC  
> Url: https://github.com/boostorg/winapi/issues/70#issuecomment-398049822  

Do I understand correctly that Windows CE does not use dllimport in Windows SDK, ever?  
  
If so, I think the general approach should be something like this:  
  
- Use dllimport by default everywhere but WinCE. This should be covered by a default macro like `BOOST_WINAPI_IMPORT` which is equivalent to `BOOST_SYMBOL_IMPORT` except for WinCE, where it should be empty.  
- Other unconventional platforms (is it Windows Mobile only?) should use the specialized macros (e.g. `BOOST_WINAPI_IMPORT_EXCEPT_WM`), which expand to `BOOST_SYMBOL_IMPORT` on all platforms except CE and the associated platform.  
  
Any future extensions to Boost.WinAPI will use `BOOST_WINAPI_IMPORT`, the default macro. If it breaks the other unconventional platforms then someone interested will have to create a PR to change the markup to the proper macro.
