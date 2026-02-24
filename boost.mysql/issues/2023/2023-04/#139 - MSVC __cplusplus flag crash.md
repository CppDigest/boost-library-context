# #139 - MSVC __cplusplus flag crash [Closed]

> Username: H1X4Dev  
> Created at: 2023-04-04 15:17:46 UTC  
> Updated at: 2023-05-24 10:19:12 UTC  
> Closed at: 2023-05-24 10:19:11 UTC  
> Url: https://github.com/boostorg/mysql/issues/139  

Hello,  
  
When using LivePlusPlus (Hot-Reload library) we have noticed that the `__cplusplus` flag that is included [here](https://github.com/boostorg/mysql/blob/master/CMakeLists.txt#L119) will make the compiler compiler create 2 different functions for consumer and producer (not sure if I worded this right).  
  
Logic is basically:  
- MySQL: Defines [__cplusplus](https://github.com/boostorg/mysql/blob/master/CMakeLists.txt#L119)  
- Application A: Consumes MySQL, but does not define `__cplusplus`  
  
Result: Allocator/Deallocator uses wrong function. Does it work fine without hot-reloading? Yes, because that flag is never defined in first place, so the Application proceeds to call its own thing.  
  
![image](https://user-images.githubusercontent.com/10332146/229836913-99a5e000-6fdb-4b3a-ba19-0b7c9b09fb0b.png)  
  
While the above image is correct for my application, after hot-reload what will happen is that it will compile with Boost.MySQL `__cplusplus` flag and cause the program to use a different deallocator resulting in crash.  
  
Before:  
![image](https://user-images.githubusercontent.com/10332146/229837384-5c76734e-0018-4140-a47e-57d4b0d0a391.png)  
  
After Reload:  
![image](https://user-images.githubusercontent.com/10332146/229837436-63470a15-8591-4f33-8c86-2eafe81adfb2.png)  
  
This function should have never been called from the Application.  
![image](https://user-images.githubusercontent.com/10332146/229837601-72f3f3dd-a429-401a-b076-48f201d75b73.png)  
  
I don't know what is the appropriate fix for this issue, but in my case defining the [macro](https://github.com/boostorg/mysql/blob/master/CMakeLists.txt#L119) will fix my issues, however note that a typical user will probably never know about it's existence and will just have undefined behavior.

---

## Comment 1

> Username: friendlyanon  
> Created at: 2023-04-04 15:29:52 UTC  
> Updated at: 2023-04-04 15:42:06 UTC  
> Url: https://github.com/boostorg/mysql/issues/139#issuecomment-1496185896  

> I don't know what is the appropriate fix for this issue  
  
Ideally, MS would finally report the C++ version properly.  
  
In reality: https://learn.microsoft.com/en-us/cpp/build/reference/zc-cplusplus?view=msvc-170    
This flag was added in Visual Studio 2017 version 15.7, for which the corresponding CMake variable `MSVC_VERSION` reports a value equal to `_MSC_VER`, [which is `1914`](https://learn.microsoft.com/en-us/cpp/preprocessor/predefined-macros?view=msvc-170).  
  
Drone runs with MSVC 14.1, which I assume to be Visual Studio 2017 version 15.0. I'm not sure what the support policy here is, but using `__cplusplus` won't work on all supported (or at least tested) platforms.  
  
Also as a curiosity, one MSVC FE dev has mentioned Boost at being liable for not properly handling this issue: https://old.reddit.com/r/cpp/comments/bqsyss/zc_cplusplus_by_default/eo8i389/

---

## Comment 2

> Username: H1X4Dev  
> Created at: 2023-04-04 16:31:48 UTC  
> Updated at: 2023-04-04 16:50:04 UTC  
> Url: https://github.com/boostorg/mysql/issues/139#issuecomment-1496273926  

Interestingly enough, the [article](https://devblogs.microsoft.com/cppblog/msvc-now-correctly-reports-__cplusplus) states:  
```  
We’ll continue to require use of the /Zc:__cplusplus switch for all minor versions of MSVC in the 19.xx family.  
```  
  
I also found that there is an [issue](https://gitlab.kitware.com/cmake/cmake/-/issues/18837) raised to make this compiler option on by default on CMake.  
  
From seeing [Qt issue](https://codereview.qt-project.org/c/qt/qtbase/+/334623) it seems like they are enforcing users to have it enabled.  Perhaps something like  
  
```  
#if defined(_MSC_VER) && _MSC_VER >= 1914 && __cplusplus < 201103L   
# error "Please define /Zc:__cplusplus flag in your application."  
#endif  
```  
  
would be great to have to avoid surprises.

---

## Comment 3

> Username: anarthal  
> Created at: 2023-04-04 16:59:03 UTC  
> Url: https://github.com/boostorg/mysql/issues/139#issuecomment-1496306906  

Hi @H1X4Dev,  
  
Thanks for reporting the issue and for the detailed information you provided. IIRC, that's defined because Asio used to base one of its BOOST_ASIO_HAS_XXX on this macro. I think this is no longer the case, and that the definition should be safe to remove. If CIs report that this is the case, I will ship the change in Boost 1.83.  
  
Regards,  
Ruben.
