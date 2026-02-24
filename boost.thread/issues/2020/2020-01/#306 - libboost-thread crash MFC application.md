# #306 - libboost-thread crash MFC application [Open]

> Username: sirc-yuv  
> Created at: 2020-01-26 13:42:36 UTC  
> Updated at: 2020-01-28 17:43:34 UTC  
> Url: https://github.com/boostorg/thread/issues/306  

Hello,  
I have built boost 1.71 static libraries to be used inside an MFC dll (windows 10) .  
I got all libboost ready and it's all OK when used regex.  
The presence of liboost-thread-vc140-mt-gd-x64-1_71 in the "additional libraries" in VS list in project settings causes a crash on loading the MFC dll that uses regex, even if Boost.Thread is not used, and headers not included. Just the presence in the list - cases a crash in the AfxCoreInitModule(); in MFC code. This probably has something to do with the AFX_MANAGE_STATE in MFC...  
Can someone please help?   
build command line s follows:  
.\b2.exe  -j8 toolset=msvc-14.0 address-model=64 architecture=x86 stage link=static threading=multi runtime-link=shared --build-type=complete stage cxxflags="/D_AFXDLL"  
Thanks for any help!

---

## Comment 1

> Username: viboes  
> Created at: 2020-01-28 17:43:34 UTC  
> Url: https://github.com/boostorg/thread/issues/306#issuecomment-579370624  

I'm sorry. I don't have a Windows machine, and I don't know nothing at all about windows.
