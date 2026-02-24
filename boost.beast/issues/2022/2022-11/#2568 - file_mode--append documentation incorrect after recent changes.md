# #2568 - file_mode::append documentation incorrect after recent changes. [Closed]

> Username: Anulap  
> Created at: 2022-11-28 10:05:17 UTC  
> Updated at: 2023-02-02 06:00:21 UTC  
> Closed at: 2023-02-02 06:00:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2568  

https://github.com/boostorg/beast/commit/6bf9c882a484d225718bf97389e27f0346ec9a22  
  
Due to that commit (and similar for other platforms), the behaviour of file_mode::append has changes. Now it does append to the end of the file instead of truncation. So the documentation should reflect that. Instead of   
  
>         The current file position shall be set to the end of  
>         the file prior to each write.  
>   
>         @li If the file does not exist, it is created.  
>   
>         @li If the file exists, it is truncated to  
>         zero size upon opening.  
  
It should read  
  
>         The current file position shall be set to the end of  
>         the file prior to each write.  
>   
>         If the file does not exist, it is created.
