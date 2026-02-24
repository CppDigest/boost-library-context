# #25 Use previous 'temp_directory_path' code for WinCE [Closed]

> Username: muggenhor  
> Created at: 2015-12-16 17:15:24 UTC  
> Updated at: 2020-05-04 22:56:50 UTC  
> Closed at: 2020-05-04 22:56:50 UTC  
> Url: https://github.com/boostorg/filesystem/pull/25  

Use older Windows code for temp_directory_path on Windows CE. This is necessary because of Windows CE's lack of environment variables, which the new Windows implementation requires.

---
