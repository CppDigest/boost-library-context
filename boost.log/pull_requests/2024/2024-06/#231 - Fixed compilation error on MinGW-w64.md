# #231 Fixed compilation error on MinGW-w64. [Merged]

> Username: hiiiik  
> Created at: 2024-06-11 14:47:45 UTC  
> Updated at: 2024-06-13 18:06:42 UTC  
> Merged at: 2024-06-13 18:06:42 UTC  
> Closed at: 2024-06-13 18:06:42 UTC  
> Url: https://github.com/boostorg/log/pull/231  

The default behavior of _windres.exe_ to read the output of the preprocessor is using popen. However, the popen implementation may be buggy on some non-English hosts, which would cause a compilation error like this:  
  
```  
windres.exe: can't open file `page:': Invalid argument  
```  
  
It can be fixed by adding **--use-temp-file** option and _windres.exe_ will use a temporary file to read instead of using opoen. I also tried compiling with the option on English-only locale and it also works fine, so it is probably a good idea to add it.

---

## Comment 1

> Username: Lastique  
> Created_at: 2024-06-11 17:03:11 UTC  
> Url: https://github.com/boostorg/log/pull/231#issuecomment-2161233072  

Should [Boost.Build](https://github.com/bfgroup/b2/) be modified the same way?

---

## Comment 2

> Username: hiiiik  
> Created_at: 2024-06-12 14:15:03 UTC  
> Updated_at: 2024-06-12 14:15:22 UTC  
> Url: https://github.com/boostorg/log/pull/231#issuecomment-2163127866  

> Should [Boost.Build](https://github.com/bfgroup/b2/) be modified the same way?  
  
I haven't tried compiling this library through b2, don't known if it can work or not. I will test it tomorrow, BTW, bootstrap of b2 itself does not work because of the same problem, I'd like to create a PR for that later.

---

## Comment 3

> Username: hiiiik  
> Created_at: 2024-06-13 15:39:20 UTC  
> Url: https://github.com/boostorg/log/pull/231#issuecomment-2166035792  

The error doesn't occur if the library is built with b2. Support for the event log is actually disable since b2 only support mc.exe but not GNU's windmc.exe.

---

## Comment 4

> Username: Lastique  
> Created_at: 2024-06-13 18:06:35 UTC  
> Url: https://github.com/boostorg/log/pull/231#issuecomment-2166469258  

Ok, thanks for checking. And for the PR.

---
