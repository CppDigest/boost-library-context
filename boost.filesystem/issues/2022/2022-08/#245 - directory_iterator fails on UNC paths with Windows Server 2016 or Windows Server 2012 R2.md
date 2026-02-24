# #245 - directory_iterator fails on UNC paths with Windows Server 2016 or Windows Server 2012 R2 [Closed]

> Username: dlaugt  
> Created at: 2022-08-03 10:30:35 UTC  
> Updated at: 2022-08-25 15:44:56 UTC  
> Closed at: 2022-08-08 14:22:36 UTC  
> Url: https://github.com/boostorg/filesystem/issues/245  

I've upgraded boost from 1.67.0 to 1.79.0. I compile the following example with Visual Studio 2017. When I run the program, it fails with Windows Server 2016 or Windows Server 2012 R2:  
```cpp  
#include <boost/filesystem/operations.hpp>`  
#include <iostream>  
  
int main ()  
{  
  try  
  {  
    boost::filesystem::directory_iterator it ("\\\\host\\dir");  
  }  
  catch (std::exception& ex)  
  {  
    std::cout << "Exception: " << ex.what () << std::endl;  
  }  
  
  return 0;  
}  
```  
Here is the output:  
`Exception: boost::filesystem::directory_iterator::construct: The parameter is incorrect [system:87]: "\\host\dir"`  
  
It's a valid UNC path (eg the directory exists). The same program works with Windows Server 2019 or Windows 10.  
  
It doesn't work with Boost 1.79.0 and the latest code in develop branch. It works fine with Boost 1.67.0.  
  
In dir_itr_create(), it iterates to the following paths without any success (error code = ERROR_INVALID_PARAMETER):  
  - get_file_information_by_handle_ex with file_id_extd_directory_restart_info_class  
  - get_file_information_by_handle_ex with file_full_directory_restart_info_class  
  - get_file_information_by_handle_ex with file_id_both_directory_restart_info_class  
  
Any idea of what could be wrong?

---

## Comment 1

> Username: Lastique  
> Created at: 2022-08-03 11:58:36 UTC  
> Url: https://github.com/boostorg/filesystem/issues/245#issuecomment-1203855029  

The problem is apparently Windows Server not supporting its own damn APIs for UNC paths. Could you test if the code from `feature/fix_win_server_2016_unc_paths` branch works?

---

## Comment 2

> Username: dlaugt  
> Created at: 2022-08-03 14:20:23 UTC  
> Url: https://github.com/boostorg/filesystem/issues/245#issuecomment-1204014865  

Unfortunately, it doesn't help. It goes to nt_query_directory_file () with file_directory_information_class but fails with the same error code:  
`Exception: boost::filesystem::directory_iterator::construct: The parameter is incorrect [system:87]: "\\host\dir"`

---

## Comment 3

> Username: Lastique  
> Created at: 2022-08-03 15:12:05 UTC  
> Url: https://github.com/boostorg/filesystem/issues/245#issuecomment-1204080157  

In that case, I'm sorry, but your use case won't be supported. I'm not aware of any other way to iterate over a directory that involves a handle and `FindFirstFileW`/`FindNextFileW` is incompatible with handles. Using a handle is necessary for the `remove_all` implementation.

---

## Comment 4

> Username: dlaugt  
> Created at: 2022-08-03 17:11:31 UTC  
> Url: https://github.com/boostorg/filesystem/issues/245#issuecomment-1204245167  

Ok but this is a regression, it was supported before... I've tried to map the UNC path into a drive (eg Z:\dir) but I have the same error.  
  
If I activate the C++17 mode and compile the program with std::filesystem (Microsoft implementation), it works fine on Windows Server 2016 and Windows Server 2012 R2.  
  
However, I would like to stay on the default C++ standard (e.g. C++14) and still use boost...

---

## Comment 5

> Username: dlaugt  
> Created at: 2022-08-03 17:21:09 UTC  
> Updated at: 2022-08-03 18:18:40 UTC  
> Url: https://github.com/boostorg/filesystem/issues/245#issuecomment-1204255951  

Microsoft is using `FindFirstFileW`/`FindNextFileW` certainly for a good reason:  
https://github.com/microsoft/STL/blob/main/stl/src/filesystem.cpp

---

## Comment 6

> Username: Lastique  
> Created at: 2022-08-03 18:05:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/245#issuecomment-1204304254  

We used to use `FindFirstFileW`/`FindNextFileW`, and the implementation is still there for Win CE, but that implementation does not allow `remove_all` to be protected against [CVE-2022-21658](https://www.cve.org/CVERecord?id=CVE-2022-21658). (And yes, Microsoft's implementation is vulnerable.)  
  
I could probably merge `FindFirstFileW`/`FindNextFileW` implementation with `GetFileInformationByHandleEx`/`NtQueryDirectoryFile` based one, but that further complicates both the directory iterator and `remove_all` implementations. I suppose, I'll leave this open and try and see how ugly this will get.

---

## Comment 7

> Username: Lastique  
> Created at: 2022-08-08 10:08:19 UTC  
> Url: https://github.com/boostorg/filesystem/issues/245#issuecomment-1207924795  

@dlaugt Could you test if https://github.com/boostorg/filesystem/pull/246 helps in your case?

---

## Comment 8

> Username: dlaugt  
> Created at: 2022-08-25 15:44:56 UTC  
> Url: https://github.com/boostorg/filesystem/issues/245#issuecomment-1227451001  

The change in #246 resolves my issue. Excellent, thanks a lot.
