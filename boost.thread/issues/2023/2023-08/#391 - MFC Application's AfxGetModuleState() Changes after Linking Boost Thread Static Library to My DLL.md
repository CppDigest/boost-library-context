# #391 - MFC Application's AfxGetModuleState() Changes after Linking Boost Thread Static Library to My DLL [Open]

> Username: ZhongJunhao  
> Created at: 2023-08-21 08:43:28 UTC  
> Updated at: 2023-08-21 08:43:28 UTC  
> Url: https://github.com/boostorg/thread/issues/391  

Issue Description:  
  
Background:  
I have developed a program that functions as an MFC (Microsoft Foundation Classes) DLL. In this program, I linked the Boost Thread static library to my DLL.  
  
Problem:  
Upon loading my DLL into an MFC application, I have noticed an unexpected behavior. The function AfxGetModuleState() within the MFC application undergoes a modification. Specifically, the global app and resource handler data, among other things, seem to transform into data originating from my DLL.  
  
Expected Behavior:  
The AfxGetModuleState() function within the MFC application should maintain its standard behavior and not be influenced by the linked Boost Thread static library in my DLL.  
  
Steps to Reproduce:  
  
Create an MFC application.  
Link my DLL, which contains the Boost Thread static library, to the application.  
Load the DLL within the MFC application.  
Observe the change in behavior of AfxGetModuleState() and the unexpected alteration of global data.  
Additional Information:  
  
DLL Type: MFC-based  
Linked Library: Boost Thread (Static)  
MFC Application Type: MFC-based  
Environment: Windows  
Compiler: vs2010  
Boost Library Version: 1.73 and 1.78  
Impact:  
This issue is causing unexpected behavior in MFC applications when integrating the Boost Thread static library through my MFC DLL. As a result, it is essential to address this issue to ensure the proper functioning of MFC applications using this DLL configuration.  
  
Your assistance is greatly appreciated.
