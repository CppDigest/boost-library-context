# #5 jump and make stubs for arm64 (aarch64) for iOS ABI [Merged]

> Username: johncapfull  
> Created at: 2014-12-30 14:31:55 UTC  
> Updated at: 2015-05-03 13:14:52 UTC  
> Merged at: 2014-12-30 20:03:31 UTC  
> Closed at: 2014-12-30 20:03:31 UTC  
> Url: https://github.com/boostorg/context/pull/5  

Hello, Oliver.  
  
I have implemented make and jump context trampolines for ARM64 architecture. Original stubs are not not suitable since ABI and instruction sets of armv7 and A64 are much different. I have tested these stubs on IPad Mini Retina and iPhone 6+ (compiled using Apple LLVM 5.1 (Xcode 5.1)), all unit tests passed successfully.  
  
Feel free to ask any questions. And thank you for useful library!  
## Resources  
  
**ARMv8 instruction set**  
http://www.element14.com/community/servlet/JiveServlet/previewBody/41836-102-1-229511/ARM.Reference_Manual.pdf  
  
**AArch64 Procedure call standard**  
http://infocenter.arm.com/help/topic/com.arm.doc.ihi0055b/IHI0055B_aapcs64.pdf

---

## Comment 1

> Username: olk  
> Created_at: 2014-12-30 20:03:33 UTC  
> Url: https://github.com/boostorg/context/pull/5#issuecomment-68392293  

Hello Evgeny,  
thank you for your patches!  
I wish you a happy new year,  
Oliver

---

## Comment 2

> Username: gdochev  
> Created_at: 2015-05-03 13:14:52 UTC  
> Url: https://github.com/boostorg/context/pull/5#issuecomment-98479755  

Hi Evgeny, could you land a hand? I am having trouble compiling this stub. Details are in issue #14.  
Best Regards,  
George

---
