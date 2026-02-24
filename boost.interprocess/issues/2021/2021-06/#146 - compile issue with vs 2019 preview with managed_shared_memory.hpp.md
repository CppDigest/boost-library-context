# #146 - compile issue with vs 2019 preview with managed_shared_memory.hpp [Closed]

> Username: vautieri  
> Created at: 2021-06-10 20:17:08 UTC  
> Updated at: 2024-10-03 08:12:54 UTC  
> Closed at: 2024-10-03 08:12:54 UTC  
> Url: https://github.com/boostorg/interprocess/issues/146  

Two issues...  
  
interprocess\detail\win32_api.hpp(1491): error C2143: syntax error: missing ')' before 'module'  
  
which is  
inline bool free_library(hmodule module)  
{  return 0 != FreeLibrary(module); }  
  
if I rename "module" in the complete file to something like "modulein",  I get past this.  Not sure why module is defined already to something that acts as a keyword.  
  
  
then once you get past that, issue 2, unicode is getting mixed with char *, so you get build errors here as winapi::unlink_file is char*.  This is just one of many examples.    
  
inline bool delete_file(const wchar_t *namein)  
{  return winapi::unlink_file(namein);   }  
  
I was trying to use boost 1.71.0 as supposedly 2019 can build interprocess code, which mine did not (probably because the the "module" word acting like a keyword, so I went to 1.76.0.  So  I started to debug on this version which is what I've documented above.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-08-09 21:33:14 UTC  
> Url: https://github.com/boostorg/interprocess/issues/146#issuecomment-895565479  

I could not reproduce your problem, can you provide a minimal example and the compilation flags you are using on VS2019?

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-10-03 08:12:54 UTC  
> Url: https://github.com/boostorg/interprocess/issues/146#issuecomment-2390793831  

Closing the issue as there was no further info.
