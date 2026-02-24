# #41 - Need SetFilePointerEx [Closed]

> Username: vinniefalco  
> Created at: 2017-07-06 08:10:43 UTC  
> Updated at: 2017-07-06 14:38:43 UTC  
> Closed at: 2017-07-06 12:25:40 UTC  
> Url: https://github.com/boostorg/winapi/issues/41  

I'll add it, this is a reminder

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-06 08:34:17 UTC  
> Url: https://github.com/boostorg/winapi/issues/41#issuecomment-313332304  

This is not as easy as I thought, I added these two items in the correct place:  
  
```  
BOOST_SYMBOL_IMPORT boost::detail::winapi::BOOL_ WINAPI  
SetFilePointerEx(  
    boost::detail::winapi::HANDLE_ hFile,  
    boost::detail::winapi::LARGE_INTEGER_ lpDistanceToMove,  
    boost::detail::winapi::PLARGE_INTEGER_ lpNewFilePointer,  
    boost::detail::winapi::DWORD_ dwMoveMethod);  
```  
  
and  
  
```  
using ::SetFilePointerEx;  
```  
  
But I get an error:  
  
```  
1>C:\Program Files (x86)\Windows Kits\10\Include\10.0.15063.0\um\fileapi.h(1112): error C2733: 'SetFilePointerEx': second C linkage of overloaded function not allowed  
1>C:\Users\vinnie\lib\boost_1_64_0\boost/detail/winapi/file_management.hpp(162): note: see declaration of 'SetFilePointerEx'  
```  
  
Oddly, `SetFilePointer` works fine and they look quite similar.

---

## Comment 2

> Username: Lastique  
> Created at: 2017-07-06 12:25:40 UTC  
> Url: https://github.com/boostorg/winapi/issues/41#issuecomment-313381290  

It can't be added without including `windows.h` because the function takes `LARGE_INTEGER` by value, which requires it to be defined. It cannot be defined in Boost.WinAPI because that will result in conflicts when `windows.h` is also included.  
  
The error you're seeing is because Windows SDK declares the function with `::LARGE_INTEGER` type and `boost::detail::winapi::LARGE_INTEGER_` is a different type (although declared to have the same binary layout as `::LARGE_INTEGER`). It works for other functions that take `LARGE_INTEGER` by pointer because we can just cast the pointer before forwarding the call, but it doesn't work if the function takes it by value.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-06 12:35:53 UTC  
> Url: https://github.com/boostorg/winapi/issues/41#issuecomment-313383470  

How do you feel about this  
```  
inline  
boost::detail::winapi::BOOL_  
set_file_pointer_ex(  
    boost::detail::winapi::HANDLE_ hFile,  
    boost::detail::winapi::LARGE_INTEGER_ lpDistanceToMove,  
    boost::detail::winapi::PLARGE_INTEGER_ lpNewFilePointer,  
    boost::detail::winapi::DWORD_ dwMoveMethod)  
{  
    auto dwHighPart = lpDistanceToMove.u.HighPart;  
    auto dwLowPart = boost::detail::winapi::SetFilePointer(  
        hFile,  
        lpDistanceToMove.u.LowPart,  
        &dwHighPart,  
        dwMoveMethod);  
    if(dwLowPart == boost::detail::winapi::INVALID_SET_FILE_POINTER_)  
        return 0;  
    if(lpNewFilePointer)  
    {  
        lpNewFilePointer->u.LowPart = dwLowPart;  
        lpNewFilePointer->u.HighPart = dwHighPart;  
    }  
    return 1;  
}  
```

---

## Comment 4

> Username: Lastique  
> Created at: 2017-07-06 14:24:19 UTC  
> Updated at: 2017-07-06 14:24:37 UTC  
> Url: https://github.com/boostorg/winapi/issues/41#issuecomment-313411543  

This doesn't work if the position is beyond 4 GiB, right? This may be ok for some applications and not ok for others.  
  
In general, Boost.WinAPI does not contain any logic or tries to emulate the missing API. I think this kind of code is better placed in the user's code.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-07-06 14:38:37 UTC  
> Updated at: 2017-07-06 14:38:43 UTC  
> Url: https://github.com/boostorg/winapi/issues/41#issuecomment-313415865  

It should work for large files. I agree that Boost should not try to emulate it - thanks.
