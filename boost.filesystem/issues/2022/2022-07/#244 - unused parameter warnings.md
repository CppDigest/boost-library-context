# #244 - unused parameter warnings [Closed]

> Username: vinniefalco  
> Created at: 2022-07-24 13:37:03 UTC  
> Updated at: 2022-07-24 21:16:31 UTC  
> Closed at: 2022-07-24 14:06:23 UTC  
> Url: https://github.com/boostorg/filesystem/issues/244  

my sources are a tiny bit out of date so the line number might not match  
```  
>boost_url.vcxproj -> C:\Users\vinnie\src\boost\libs\url\bin64\Debug\boost_url.lib  
11>C:\Users\vinnie\src\boost\libs\filesystem\src\directory.cpp(190,78): warning C4100: 'extra_size': unreferenced formal parameter  
11>C:\Users\vinnie\src\boost\libs\filesystem\src\operations.cpp(2443,20): warning C4100: 'from_handle': unreferenced formal parameter  
11>C:\Users\vinnie\src\boost\libs\filesystem\src\operations.cpp(2442,19): warning C4100: 'callback_reason': unreferenced formal parameter  
11>C:\Users\vinnie\src\boost\libs\filesystem\src\operations.cpp(2441,19): warning C4100: 'stream_number': unreferenced formal parameter  
11>C:\Users\vinnie\src\boost\libs\filesystem\src\operations.cpp(2438,27): warning C4100: 'total_bytes_transferred': unreferenced formal parameter  
11>C:\Users\vinnie\src\boost\libs\filesystem\src\operations.cpp(2437,27): warning C4100: 'total_file_size': unreferenced formal parameter  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2022-07-24 14:06:23 UTC  
> Url: https://github.com/boostorg/filesystem/issues/244#issuecomment-1193325510  

Should be fixed already.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-07-24 14:26:31 UTC  
> Url: https://github.com/boostorg/filesystem/issues/244#issuecomment-1193328956  

Still there in _develop_  
https://github.com/boostorg/filesystem/blob/ea22e7655258b61776e4213ca4001a6b97b3541b/src/directory.cpp#L192

---

## Comment 3

> Username: Lastique  
> Created at: 2022-07-24 21:16:31 UTC  
> Url: https://github.com/boostorg/filesystem/issues/244#issuecomment-1193395666  

The fix is to [disable](https://github.com/boostorg/filesystem/blob/ea22e7655258b61776e4213ca4001a6b97b3541b/include/boost/filesystem/detail/header.hpp#L22) the warning. I will not be removing parameter names.
