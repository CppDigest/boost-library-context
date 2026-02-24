# #286 - More directory_iterator errors [Closed]

> Username: cdecker08  
> Created at: 2023-05-11 14:22:33 UTC  
> Updated at: 2023-05-15 14:59:48 UTC  
> Closed at: 2023-05-13 18:06:57 UTC  
> Url: https://github.com/boostorg/filesystem/issues/286  

We have a handful of users who are encountering errors with directory iterator when dealing with more ... unique drive configurations.  All of these configurations fail when passing `file_id_extd_directory_restart_info_class` (directory.cpp:815) but succeed if allowed to fallback to passing `file_full_directory_restart_info_class` (directory.cpp:859).  It feels like any error should trigger the next fallback, however I realize that may make error reporting more difficult.  I have included the below table which indicates the drive configuration along with the error code they received:  
  
<html>  
  
  
<body>  
  
Use Case | Windows ErrorCode  
-- | --  
Network drive - Fritzbox 7490 Firmware 7.29 | 31 - ERROR_GEN_FAILURE A device attached to the system is not functioning  
APFS Formatted Drive (Paragon’s APFS For Windows (2.1.110) ) | 1 - ERROR_INVALID_FUNCTION Incorrect Funtion  
DrivePool | 1359 - ERROR_INTERNAL_ERROR An internal error occurred.  
DrivePool + VirtualBox + Symlinks | 1359 - ERROR_INTERNAL_ERROR An internal error occurred.  
VirtualBox + Symlinks | 1359 - ERROR_INTERNAL_ERROR An internal error occurred.  
  
</body>  
  
</html>  
  
Please let me know if you need more info or if I can be of any further help.

---

## Comment 1

> Username: Lastique  
> Created at: 2023-05-13 13:10:36 UTC  
> Url: https://github.com/boostorg/filesystem/issues/286#issuecomment-1546649118  

Thank you for the report and the list of error codes. Could you test if 615881f works for you?

---

## Comment 2

> Username: cdecker08  
> Created at: 2023-05-15 14:59:48 UTC  
> Url: https://github.com/boostorg/filesystem/issues/286#issuecomment-1548032124  

I'll get a build to some of our users and have out Customer Service team keep an eye out for additional error codes.
