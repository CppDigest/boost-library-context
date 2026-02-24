# #114 Update MSC version check for VS2017RTW [Merged]

> Username: aargor  
> Created at: 2017-02-21 21:42:40 UTC  
> Updated at: 2017-02-24 14:08:14 UTC  
> Merged at: 2017-02-24 14:08:14 UTC  
> Closed at: 2017-02-24 14:08:14 UTC  
> Url: https://github.com/boostorg/thread/pull/114  



---

## Comment 1

> Username: aargor  
> Created_at: 2017-02-21 21:42:52 UTC  
> Url: https://github.com/boostorg/thread/pull/114#issuecomment-281490636  

Hello,  
  
I'm Aaron Gorenstein, a software engineer on the Microsoft Visual C++ compiler. We've included a lot of Boost into our rolling "real-world" tests to improve our conformance and correctness. We've sometimes found the need to patch our local enlistments, and with the upcoming VS2017 release we're trying to give those contributions back.  
  
This PR simply adjust the version number in a windows-specific chunk of code. The new compiler version is going to be 1910, so the #ifdef goes down a different path between VS2015 and the upcoming VS2017. It seems someone's already hit this in the RC, and we've also found that this (or equivalent) is a necessary change to pass the thread tests. It seems there's already been some discussion about the topic here: https://github.com/boostorg/thread/pull/87.  
  
I hope this is a sufficient explanation and solution. If there's anything else I can do, let me know.  
  
Thanks,  
Aaron Gorenstein  
Microsoft Visual C++

---

## Comment 2

> Username: viboes  
> Created_at: 2017-02-22 14:55:21 UTC  
> Updated_at: 2017-02-22 16:00:02 UTC  
> Url: https://github.com/boostorg/thread/pull/114#issuecomment-281691806  

Hi, I don't know too much about Microsoft Visual C++ compiler.  
I suspect that the last patch intended to work for versions > 1900 with the code  
  
```          
              typedef int (__cdecl *_PVFV)();  
              #define INIRETSUCCESS 0  
              #define PVAPI int __cdecl  
```  
  
Is there a version > 2000?  
If yes which code applies?

---

## Comment 3

> Username: aargor  
> Created_at: 2017-02-22 17:06:08 UTC  
> Url: https://github.com/boostorg/thread/pull/114#issuecomment-281733562  

Hi,  
  
To answer your question directly: VS2017 has the highest current version number, at 1910. There is no version number > 2000.  
  
To address your suspicion: when the patch was introduced, there was no version number > 1900. In other words, at the time it was committed, I believe the check _MSC_VER > 1900 always failed on any available VS version.  
  
Now that VS2017 has a version number higher than 1900, the check succeeds. However, we've found that VS2017 still needs the "else" path of that #ifdef.  
  
This is in line with the earlier discussion: https://github.com/boostorg/thread/pull/87.  
  
Because I'm obviously a newcomer to your code-base, I've tried to make the change conservative: I've upped the version check so that it always fails again. In this way it is consistent with the previous behavior. I would be happy to remove the > 2000 check entirely, but I don't know the full context -- I didn't mean to include it to cause confusion.  
  
Thanks,  
Aaron

---

## Comment 4

> Username: viboes  
> Created_at: 2017-02-24 14:07:11 UTC  
> Url: https://github.com/boostorg/thread/pull/114#issuecomment-282299103  

Thanks for clarifications. I'm the maintainer of Boost.Thread but I have not written this code and I said I don't know too much about Microsoft Visual C++ compiler.  
  
I'll merge it the.

---
