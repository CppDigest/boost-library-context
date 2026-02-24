# #2 Fix extension adding in "search_path" (Windows) [Merged]

> Username: krabicezpapundeklu  
> Created at: 2017-04-05 09:28:40 UTC  
> Updated at: 2017-04-05 09:31:56 UTC  
> Merged at: 2017-04-05 09:31:56 UTC  
> Closed at: 2017-04-05 09:31:56 UTC  
> Url: https://github.com/boostorg/process/pull/2  

Original code checks for "program", "program.exe", "program.exe.com" and "program.exe.com.bat" instead of "program", "program.exe", "program.com" and "program.bat".  
This change fixes this.

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2017-04-05 09:31:47 UTC  
> Url: https://github.com/boostorg/process/pull/2#issuecomment-291806614  

Thanks!

---
