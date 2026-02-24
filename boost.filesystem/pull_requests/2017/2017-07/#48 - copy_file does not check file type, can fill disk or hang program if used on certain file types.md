# #48 copy_file does not check file type, can fill disk or hang program if used on certain file types [Closed]

> Username: pattonater  
> Created at: 2017-07-24 21:06:08 UTC  
> Updated at: 2020-05-01 17:10:08 UTC  
> Closed at: 2020-05-01 17:10:08 UTC  
> Url: https://github.com/boostorg/filesystem/pull/48  

Boost’s copy_file method, when passed non-regular files, can fill up the user’s disk space or hang the program.  
  
The copy_file method does not check file type before it begins copying, which can lead to unspecified behavior if the user tries to copy a non-regular (type) file. As only regular files can be properly copied by reading and writing their contents, trying to copy non-regular files in this way can cause problems. For example:  
- Trying to copy a symlink to the character device /dev/urandom with copy_file will copy random data into the output file indefinitely, quickly filling up the user’s disk.   
- Trying to copy a FIFO type file with copy_file will result in the program hanging indefinitely if left unattended.  
  
This problem can be fixed without too much work by checking the result of the post-open stat call to check that a regular file was opened.  
  
This problem was found as part of an effort to detect and deal with “environmental” bugs in popular applications (for more information, check out https://works-everywhere.org). It was found using a tool that detects situations where an application fails to correctly handle unusual environmental conditions such as files having an unexpected file type.

---

## Comment 1

> Username: Lastique  
> Created_at: 2020-05-01 14:51:17 UTC  
> Url: https://github.com/boostorg/filesystem/pull/48#issuecomment-622419076  

This change is not correct as `copy_file_api` should not throw.

---
