# #768 - breaking change with rsp file when migrating from boost 1.78 to boost 1.83 under windows/mingw64 [Closed]

> Username: rlepere  
> Created at: 2023-11-07 14:35:27 UTC  
> Updated at: 2023-11-07 14:35:43 UTC  
> Closed at: 2023-11-07 14:35:42 UTC  
> Url: https://github.com/boostorg/build/issues/768  

Hello,  
  
 I am migrating my application from boost-1.78 (b2 4.7-git) to boost-1.83 (b2 4.10-git). I am using boost build and bjam as a build tool for my application.  
  
 I am using g++ compiler (11.3) under windows environment, everything was working correctly under the old version of boost-build but I am having a problem with the new one.  
  
 It is related to the linker pass where a response file .rsp file is used because the line can be very long.  
  
 In previous version, the linker line generated was something like :  
g++ -L"c:\external\boost_1_78_0" @toto.rsp  
  
now the line is  
g++ @toto.rsp  
  
and the first line of the response file is -L"c:\external\boost_1_78_0"  
This leads to problems because g++ does not find libs correctly. The first line of the response file should be either  
-L"c:\\external\\boost_1_78_0"  
or  
-L"c:/external/boost_1_78_0"  
so that g++ can find correctly the libs.  
  
What could I do to fix the problem ? Is this a bug ?  
  
Best regards,  
  
Renaud Lepère

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2023-11-07 14:35:40 UTC  
> Url: https://github.com/boostorg/build/issues/768#issuecomment-1798672064  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
