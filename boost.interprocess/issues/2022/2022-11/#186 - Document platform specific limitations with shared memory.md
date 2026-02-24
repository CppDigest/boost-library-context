# #186 - Document platform specific limitations with shared memory [Open]

> Username: Dalzhim  
> Created at: 2022-11-11 19:49:41 UTC  
> Updated at: 2022-11-11 19:50:48 UTC  
> Url: https://github.com/boostorg/interprocess/issues/186  

It can be very hard to figure out why shared memory cannot be created on macos when getting errno=1 ("Operation not permitted") as the only information. It turns out that sandboxed applications have extra hoops to go through to use shared memory. Here are the relevant documents :  
  
* https://developer.apple.com/library/archive/documentation/Security/Conceptual/AppSandboxDesignGuide/AppSandboxInDepth/AppSandboxInDepth.html#//apple_ref/doc/uid/TP40011183-CH3-SW24  
* https://developer.apple.com/library/archive/documentation/Security/Conceptual/AppSandboxDesignGuide/AppSandboxInDepth/AppSandboxInDepth.html#//apple_ref/doc/uid/TP40011183-CH3-SW21  
* https://developer.apple.com/library/archive/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19  
  
I believe the interprocess library's documentation could point people to Apple's documentation in order to make it easier to use!
