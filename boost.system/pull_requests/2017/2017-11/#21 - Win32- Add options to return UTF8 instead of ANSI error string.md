# #21 Win32: Add options to return UTF8 instead of ANSI error string [Closed]

> Username: kghost  
> Created at: 2017-11-01 22:32:34 UTC  
> Updated at: 2018-10-09 14:12:14 UTC  
> Closed at: 2018-10-09 14:12:14 UTC  
> Url: https://github.com/boostorg/system/pull/21  

ANSI  is deprecated for years, even boost::locale doesn't support ANSI  
charset. It is harder to handle ANSI strings than UTF8 strings.

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-11-01 22:52:18 UTC  
> Url: https://github.com/boostorg/system/pull/21#issuecomment-341266924  

This affects only the `#if defined(UNDER_CE) || BOOST_PLAT_WINDOWS_RUNTIME || defined(BOOST_NO_ANSI_APIS)` path, which doesn't seem correct. It also leaves the `CP_ACP` size calculation as it was, and it seems that it would be better to unify the two, changing only the code page. Start with  
  
```  
#ifdef BOOST_SYSTEM_USE_UTF8  
UINT codepage = CP_UTF8;  
#else  
UINT codepage = CP_ACP;  
#endif  
```  
  
and leave everything else the same.  
  
It would probably be better to use this path even in the case when FormatMessageA is available, to simplify the code. Otherwise we'd have to check for `BOOST_SYSTEM_USE_UTF8` in the `#if defined(UNDER_CE) || BOOST_PLAT_WINDOWS_RUNTIME || defined(BOOST_NO_ANSI_APIS)` as well, and it would be cleaner to not have to support two separate code paths.  
  
From a wider design standpoint, I'm not sure whether to use a macro here or to provide, f.ex.  
  
```  
void boost::system::set_message_code_page( unsigned cp );  
```  
  
I'll leave the decision on that to Beman.

---

## Comment 2

> Username: kghost  
> Created_at: 2017-11-02 07:20:42 UTC  
> Updated_at: 2017-11-02 07:22:31 UTC  
> Url: https://github.com/boostorg/system/pull/21#issuecomment-341337401  

IMO, we should provide following choices controlled by macro or runtime variables:  
  
1. Call ASNI API, return ANSI string  
2. Call UNICODE API, return UCS2 wstring (which is most api used by default)  
3. Call UNICODE API, return UTF8 string  
  
As boost prefer not to use wstring, option 2 may omit.  
  
But, currently with BOOST_NO_ANSI_APIS defined, we are returning a ANSI string after calling UNICODE API. This make no sense.  
  
I'll ship a new patch, which provide option 1 and 3, also a compatibility option with a compile time warning.  
  
This will be the final result:  
  
1. Call ASNI API, return ANSI string (same behavior as BOOST_NO_ANSI_APIS undefined)  
2. Call UNICODE API, return UTF8 string (with BOOST_NO_ANSI_APIS and BOOST_SYSTEM_USE_UTF8 defined)  
3. Call UNICODE API, return ASNI string (for compatibility, issue a warning at compile time, with BOOST_NO_ANSI_APIS defined but BOOST_SYSTEM_USE_UTF8 undefined)

---

## Review 3 [Changes requested]

> Username: jeking3  
> Created_at: 2018-05-07 23:15:14 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/system/pull/21#pullrequestreview-118184196  

Typos.

📁 include/boost/system/api_config.hpp

```diff
  42 |+ # if !defined(BOOST_SYSTEM_NO_DEPRECATION_WARNING) && defined(BOOST_WINDOWS_API) && defined(BOOST_NO_ANSI_APIS) && !defined(BOOST_SYSTEM_USE_UTF8)
  43 |+ #   if defined(_MSC_VER) || defined(__BORLANDC__) || defined(__DMC__)
  44 |+ #     pragma message ("Using ASNI strings with UNICODE API is deprecated, please define BOOST_SYSTEM_USE_UTF8 to use UTF8 strings. To disable this warning message, define BOOST_SYSTEM_NO_DEPRECATION_WARNING")
```

> Username: jeking3  
> Created_at: 2018-05-07 23:14:18 UTC  
> Updated_at: 2018-05-07 23:15:14 UTC  
> Url: https://github.com/boostorg/system/pull/21#discussion_r186577678  

ANSI, not ASNI

---

📁 include/boost/system/api_config.hpp

```diff
  44 |+ #     pragma message ("Using ASNI strings with UNICODE API is deprecated, please define BOOST_SYSTEM_USE_UTF8 to use UTF8 strings. To disable this warning message, define BOOST_SYSTEM_NO_DEPRECATION_WARNING")
  45 |+ #   elif defined(__GNUC__) || defined(__HP_aCC) || defined(__SUNPRO_CC) || defined(__IBMCPP__)
  46 |+ #     warning "Using ASNI strings with UNICODE API is deprecated, please define BOOST_SYSTEM_USE_UTF8 to use UTF8 strings. To disable this warning message, define BOOST_SYSTEM_NO_DEPRECATION_WARNING"
```

> Username: jeking3  
> Created_at: 2018-05-07 23:14:31 UTC  
> Updated_at: 2018-05-07 23:15:14 UTC  
> Url: https://github.com/boostorg/system/pull/21#discussion_r186577737  

ANSI, not ASNI


---

## Comment 4

> Username: pdimov  
> Created_at: 2018-10-09 14:12:14 UTC  
> Url: https://github.com/boostorg/system/pull/21#issuecomment-428207845  

`BOOST_SYSTEM_USE_UTF8` is now respected.

---
