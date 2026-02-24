# #248 - Empty strings fail to convert to wide on Windows [Open]

> Username: Capital-Z  
> Created at: 2022-05-13 04:14:41 UTC  
> Updated at: 2023-07-03 05:09:00 UTC  
> Url: https://github.com/boostorg/process/issues/248  

In file .../process/detail/windows/locale.hpp in method windows_file_codecvt::do_in(...) MultiByteToWideChar is called with the string length and this API function fails when the length is 0, so the code raises exception later on.  
Simple and safe solution would be to skip the MultiByteToWideChar(...) call in such case.
