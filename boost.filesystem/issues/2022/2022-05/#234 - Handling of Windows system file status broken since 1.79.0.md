# #234 - Handling of Windows system file status broken since 1.79.0 [Closed]

> Username: MarcelRaad  
> Created at: 2022-05-04 07:43:54 UTC  
> Updated at: 2022-08-30 07:03:47 UTC  
> Closed at: 2022-05-09 17:01:12 UTC  
> Url: https://github.com/boostorg/filesystem/issues/234  

Since commit https://github.com/boostorg/filesystem/commit/97722a3107d136eebe9425804fe0d13eab3ce2bc, calls like these fail on Windows 10:  
`boost::filesystem::is_directory(L"C:\\System Volume Information")`  
`boost::filesystem::is_symlink(L"C:\\DumpStack.log")`  
  
This is because `CreateFileW` now fails with `ERROR_ACCESS_DENIED`, while the previously used `GetFileAttributesW` succeeded.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-05-09 15:56:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/234#issuecomment-1121282304  

Note that `std::filesystem::symlink_status` also returns "Access denied" for "C:\System Volume Information". `std::filesystem::status` succeeds. I'm not sure I like this kind of inconsistency.

---

## Comment 2

> Username: MarcelRaad  
> Created at: 2022-05-10 11:10:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/234#issuecomment-1122249604  

I can confirm that the patch works great. Thanks a lot!

---

## Comment 3

> Username: TMeindl  
> Created at: 2022-08-29 13:58:14 UTC  
> Updated at: 2022-08-29 14:01:24 UTC  
> Url: https://github.com/boostorg/filesystem/issues/234#issuecomment-1230346689  

FYI: This does still not work when the directory is on a usb device; i.e. when a USB Device is for example mounted on drive H and has a valid directory with the name "source":  
boost::filesystem::is_directory("H:\source") --> false   
std::filesystem::is_directory("H:\source") --> true

---

## Comment 4

> Username: Lastique  
> Created at: 2022-08-29 15:44:57 UTC  
> Updated at: 2022-08-29 15:45:31 UTC  
> Url: https://github.com/boostorg/filesystem/issues/234#issuecomment-1230495105  

What filesystem is H:? What Windows version? Can you debug this case?  
  
Assuming that you're not passing an `error_code` and there is no exception, WinAPI calls don't fail in your case, so this is not the same issue as the one that was originally reported here. Please open a new issue with all additional information you find.

---

## Comment 5

> Username: TMeindl  
> Created at: 2022-08-30 07:03:47 UTC  
> Url: https://github.com/boostorg/filesystem/issues/234#issuecomment-1231235510  

Ah, sorry, there where other changes in the operations.cpp file; I just used this patch for testing. I can confirm that with boost version 1.80 boost::filesystem::is_directory() works as expected.
