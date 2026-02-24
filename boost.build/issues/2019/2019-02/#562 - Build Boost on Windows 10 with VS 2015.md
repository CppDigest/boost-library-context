# #562 - Build Boost on Windows 10 with VS 2015 [Open]

> Username: ardeal  
> Created at: 2019-02-26 03:20:13 UTC  
> Updated at: 2021-05-29 18:22:21 UTC  
> Url: https://github.com/boostorg/build/issues/562  

Hi,  
  
I download boost 1.59.0 zip file from:  
https://github.com/boostorg/boost/releases  
  
and tried to build it on Windows 10 with VS 2015.   
The steps I build the code are:  
1) run VS 2015 command prompt:  VS2015 X46 Native Tools Command Prompt,  
2) go to folder:D:\code_cv\boost\boost_boost_1_59_0  
3) rum command bootstrap.bat.  
  
I got the following error:  
  
```C  
D:\code_cv\boost\boost_boost_1_59_0>bootstrap.bat  
Building Boost.Build engine  
The system cannot find the path specified.  
'.\build.bat' is not recognized as an internal or external command,  
operable program or batch file.  
  
Failed to build Boost.Build engine.  
Please consult bootstrap.log for further diagnostics.  
  
You can try to obtain a prebuilt binary from  
  
   http://sf.net/project/showfiles.php?group_id=7586&package_id=72941  
  
Also, you can file an issue at http://svn.boost.org  
Please attach bootstrap.log in that case.  
  
```  
  
I checked the folder: D:\code_cv\boost\boost_boost_1_59_0\tools\build.   
There isn't file build.bat at all.  
  
  
Thanks and Best Regards,  
Ardeal

---

## Comment 1

> Username: mclow  
> Created at: 2019-03-09 02:34:25 UTC  
> Url: https://github.com/boostorg/build/issues/562#issuecomment-471138239  

Note that 1.59.0 is 10 releases old (we're about to release 1.70).  
it was released in August 13, 2015 - less than two weeks after Windows 10 and VS 2015 were released.

---

## Comment 2

> Username: user1415  
> Created at: 2020-04-03 11:05:24 UTC  
> Url: https://github.com/boostorg/build/issues/562#issuecomment-608373375  

I tried the same even though my boost version is boost_1_72_0,but am not able to build the boost and getting the message like  
Building Boost.Build engine  
File Not Found  
Failed to build Boost.Build engine.  
Please consult bootstrap.log for further diagnostics.  
  
'cmd' is not recognized as an internal or external command,  
operable program or batch file.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 18:21:52 UTC  
> Url: https://github.com/boostorg/build/issues/562#issuecomment-850877276  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
