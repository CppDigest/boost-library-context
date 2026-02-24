# #54 - fill_system_message() ignores an error returned by winapi::format_message() [Closed]

> Username: k15tfu  
> Created at: 2018-04-28 04:44:16 UTC  
> Updated at: 2018-04-29 10:44:22 UTC  
> Closed at: 2018-04-29 10:44:22 UTC  
> Url: https://github.com/boostorg/interprocess/issues/54  

In file `boost\interprocess\errors.hpp` the function `fill_system_message()` invokes `winapi::format_message()` but then doesn't check its return value and calls `string::operator+=` with `lpMsgBuf=nullptr`.  Obviously it leads to access violation.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-04-29 10:44:22 UTC  
> Url: https://github.com/boostorg/interprocess/issues/54#issuecomment-385242163  

Thanks for the report. Using a fixed error string in case FormatMessage fails. Fixed in:  
  
https://github.com/boostorg/interprocess/commit/1baf757d9f7b22ffdac8169faebc7b01efb57bf0
