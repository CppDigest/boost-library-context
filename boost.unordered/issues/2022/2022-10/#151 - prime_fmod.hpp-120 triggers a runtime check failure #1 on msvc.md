# #151 - prime_fmod.hpp:120 triggers a runtime check failure #1 on msvc [Closed]

> Username: mpalet  
> Created at: 2022-10-26 15:30:42 UTC  
> Updated at: 2023-01-12 23:12:14 UTC  
> Closed at: 2022-11-28 17:09:16 UTC  
> Url: https://github.com/boostorg/unordered/issues/151  

detail\prime_fmod.hpp:120 triggers a runtime check failure #1 on msvc (Run-Time Check Failure #1 - A cast to a smaller data type has caused a loss of data.)   
  
Looks intentional due to the cast of boost::uint32_t(hash).   
  
It could be guarded somehow to not trigger this runtime failure when using msvc compiler.  
Microsoft suggests the following:  
  
Run-Time Check Failure #1 - A cast to a smaller data type has caused a loss of data.  If this was intentional, you should mask the source of the cast with the appropriate bitmask.  For example:    
	char c = (i & 0xFF);

---

## Comment 1

> Username: cmazakas  
> Created at: 2022-10-28 20:17:16 UTC  
> Url: https://github.com/boostorg/unordered/issues/151#issuecomment-1295423336  

Hey there, I was able to replicate your issue locally. Unfortunately, it seems like you're using `/RTCc` which I found described here: https://learn.microsoft.com/en-us/cpp/build/reference/rtc-run-time-error-checks?view=msvc-170#arguments  
  
The thing to note here is their statement:  
> Because /RTCc rejects code that conforms to the standard, it's not supported by the C++ Standard Library.  
  
So, this seems like a non-standard extension to run on code. In general, we only aim to support standards-conforming code.  
  
To accommodate this use-case regardless, I did look into disabling the checks internally using:  
```cpp  
#pragma runtime_checks( "", off )  
/* runtime checks are off in this region */  
#pragma runtime_checks( "", restore )  
```  
  
around the problematic lines.  
  
Unfortunately, it seems like msvc's pragmas don't like working with member functions of class templates. The compiler accepted where the pragmas were but did nothing to suppress the checks.  
  
If you're able to tell me where to place the pragmas to disable the checks, I can add them but until then, I'm afraid there's nothing I can do.

---

## Comment 2

> Username: greenkalx  
> Created at: 2022-11-18 13:42:44 UTC  
> Updated at: 2022-11-20 21:13:02 UTC  
> Url: https://github.com/boostorg/unordered/issues/151#issuecomment-1320010260  

I've run into this too with our projects, migrating from boost_1_65_1 to boost_1_80_0.  
(Visual Studio 2017 C++11 to VS2022 C++17)  
  
For now, I am disabling in the project settings:  
`properties > c/c++ > code generation > smaller type check /RTCc`  
In the Visual Studio .vcxproj it is labelled:  
`<SmallerTypeCheck>true</SmallerTypeCheck>`  
  
We have it enabled in 300 projects for Debug builds only, not Release.  
It's not triggered in our 40+ unit test projects.  
  
`#pragma runtime_checks` is not working here either.

---

## Comment 3

> Username: cmazakas  
> Created at: 2022-11-29 16:19:57 UTC  
> Url: https://github.com/boostorg/unordered/issues/151#issuecomment-1330902883  

Good news, the fixes have been merged into the master branch which means this should be a part of the formal 1.81 release so look for the fix in the future!

---

## Comment 4

> Username: greenkalx  
> Created at: 2022-11-29 19:43:22 UTC  
> Url: https://github.com/boostorg/unordered/issues/151#issuecomment-1331200516  

Thank you @cmazakas, look forward to having this with 1.81.  
  
We may choose to stick with 1.80 since there is another fix we want from the Known Issues list:  
https://www.boost.org/users/history/version_1_80_0.html  
https://github.com/boostorg/filesystem/issues/247  
  
Could you make this available as a patch on 1.80 as a Known Issue?

---

## Comment 5

> Username: cmazakas  
> Created at: 2022-12-14 15:45:05 UTC  
> Url: https://github.com/boostorg/unordered/issues/151#issuecomment-1351664794  

@greenkalx Sorry for the wait but we finally got that patch pushed up to the website:  
https://www.boost.org/users/history/version_1_80_0.html  
  
You can see it there. Hopefully this works. I remember testing it before submitting it but that was a little while ago by now so we'll see.  
  
Also, the Boost 1.81 release is _just_ around the corner. The fix will be in that release as well.

---

## Comment 6

> Username: greenkalx  
> Created at: 2022-12-15 03:06:47 UTC  
> Url: https://github.com/boostorg/unordered/issues/151#issuecomment-1352498486  

Thanks! I see the 1.81 release on Twitter. I'll update from 1.80.

---

## Comment 7

> Username: greenkalx  
> Created at: 2023-01-12 22:43:03 UTC  
> Url: https://github.com/boostorg/unordered/issues/151#issuecomment-1381074437  

This fix is working in 1.81

---

## Comment 8

> Username: cmazakas  
> Created at: 2023-01-12 23:12:14 UTC  
> Url: https://github.com/boostorg/unordered/issues/151#issuecomment-1381094010  

That's fantastic to hear! Thank you for confirming that it works on your machine.
