# #81 - basic_types.hpp includes <winerror.h> [Closed]

> Username: pdimov  
> Created at: 2019-11-26 17:41:58 UTC  
> Updated at: 2019-12-22 16:48:07 UTC  
> Closed at: 2019-12-22 16:47:59 UTC  
> Url: https://github.com/boostorg/winapi/issues/81  

There are reports that `<boost/system/error_code.hpp>` breaks code using `NO_ERROR`; the reason is that `winapi/error_codes.hpp` includes `winapi/basic_types.hpp` which includes `<winerror.h>` which defines all error codes as macros, including `NO_ERROR`.  
  
It's not clear why `<winerror.h>` is included; `winapi/error_codes.hpp` defines the error codes as non-macros and does not rely on the macros.

---

## Comment 1

> Username: Lastique  
> Created at: 2019-11-26 18:13:28 UTC  
> Url: https://github.com/boostorg/winapi/issues/81#issuecomment-558753818  

Every user of Boost.WinAPI must still be prepared for any macros that might be defined by Windows SDK. This is because `windows.h` may still be included, either by user or by Boost.WinAPI itself.  
  
As to why `basic_types.hpp` includes `winerror.h`, I'm not sure. Probably, this is a remnant from times when there was no `error_codes.hpp` header. I can probably remove that include, although it might break someone. I'll probably do that after the release.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-12-21 23:42:01 UTC  
> Url: https://github.com/boostorg/winapi/issues/81#issuecomment-568220243  

Reminder.

---

## Comment 3

> Username: Lastique  
> Created at: 2019-12-22 16:48:07 UTC  
> Url: https://github.com/boostorg/winapi/issues/81#issuecomment-568280667  

Thanks.
