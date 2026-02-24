# #16 - path_from_handle using GetModuleFileNameW in wrong way [Closed]

> Username: Agile86  
> Created at: 2018-02-21 14:51:44 UTC  
> Updated at: 2018-04-30 12:00:07 UTC  
> Closed at: 2018-03-04 15:56:40 UTC  
> Url: https://github.com/boostorg/dll/issues/16  

boost\dll\detail\windows\path_from_handle.hpp  
  
```  
        boost::detail::winapi::GetModuleFileNameW(handle, path_hldr,   
            DEFAULT_PATH_SIZE_);  
        ec = last_error_code();  
        if (!ec) {  
            return boost::filesystem::path(path_hldr);  
        }  
```  
The problem here is that code doesn't analyze return code. If GetLastError() was dirty before a call this code will fail even if GetModuleFileNameW() will success. Yes, on success, GetModuleFileNameW() doesn't reset last error to ERROR_SUCCESS.

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-03-04 15:59:58 UTC  
> Url: https://github.com/boostorg/dll/issues/16#issuecomment-370240151  

Many thanks for reporting the issue!

---

## Comment 2

> Username: Oberon00  
> Created at: 2018-04-15 15:17:58 UTC  
> Url: https://github.com/boostorg/dll/issues/16#issuecomment-381414145  

I don't think the fix in f04ceff181508c78d0125856ec099f8a8b5d3e32 is correct, as calling `GetLastError` does not reset the error code. Instead, as  @Agile86 said, you should check the return value of `GetModuleFileNameW` and call `GetLastError` only if the return value indicates an error (i.e. if the return value equals zero or `DEFAULT_PATH_SIZE_` in this case).  
  
I think, even setting the error code to zero beforehand is not guaranteed to work as the function might internally cause and handle an error reported via `GetLastError` without setting the error code to zero afterwards.

---

## Comment 3

> Username: muggenhor  
> Created at: 2018-04-30 12:00:07 UTC  
> Url: https://github.com/boostorg/dll/issues/16#issuecomment-385378155  

The chosen solution definitely doesn't work:  
```cpp  
#include <assert.h>  
#include <windows.h>  
  
int main()  
{  
  SetLastError(42u);  
  GetLastError(); // <-- we depend on this resetting the error code (it doesn't)  
  assert(GetLastError() == ERROR_SUCCESS);  
  return 0;  
}  
```  
  
Leads to:  
```console  
Assertion failed!  
  
Program: Z:\home\vanschig\git\novaui\thirdparty\test.exe  
File: test.cpp, Line 8  
  
Expression: GetLastError() == ERROR_SUCCESS  
  
abnormal program termination  
```
