# #288 GHA: split MINGW32 test [Closed]

> Username: sdarwin  
> Created at: 2024-09-27 20:40:09 UTC  
> Updated at: 2024-10-01 18:03:50 UTC  
> Closed at: 2024-10-01 18:03:49 UTC  
> Url: https://github.com/boostorg/unordered/pull/288  

Last week I increased the memory of the hosted windows GHA runners by adding a 10GB Windows Pagefile.  
One MINGW32 test is still failing.   
What would happen if the job is divided into smaller parts. Testing. Observe GHA results.

---

## Comment 1

> Username: sdarwin  
> Created_at: 2024-09-28 00:51:22 UTC  
> Url: https://github.com/boostorg/unordered/pull/288#issuecomment-2380325026  

Increased Windows Pagefile to 20GB.   
MINGW64 has been passing.  
MINGW32 + release is also passing.  
MINGW32 + debug out of memory.  
Setting MINGW section back to standard github hosted runners 'windows-latest'.

---

## Comment 2

> Username: sdarwin  
> Created_at: 2024-09-30 23:18:22 UTC  
> Url: https://github.com/boostorg/unordered/pull/288#issuecomment-2384378901  

A newer kernel on the runners leads to sanitizer errors such as   
https://stackoverflow.com/questions/77850769/fatal-threadsanitizer-unexpected-memory-mapping-when-running-on-linux-kernels  
  
Now testing newer compilers to solve that. Will submit another pull request, soon.

---

## Comment 3

> Username: sdarwin  
> Created_at: 2024-10-01 18:03:49 UTC  
> Url: https://github.com/boostorg/unordered/pull/288#issuecomment-2386633354  

opening another pr

---
