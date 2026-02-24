# #795 - sorting issue with AMD Hawaii GPUs [Closed]

> Username: ymhsieh  
> Created at: 2018-09-10 09:37:17 UTC  
> Updated at: 2019-01-22 06:44:35 UTC  
> Closed at: 2019-01-22 06:44:35 UTC  
> Url: https://github.com/boostorg/compute/issues/795  

Dear Maintainer,   
  
I faced issues with sorting on GPU based on AMD Hawaii architecture.  The issue was reported to AMD (https://community.amd.com/thread/231056), and they suggested I should report the issue to here.  So is it possible for you to locating the issue for Hawaii GPUs?   
  
Thanks,  
Yo-Ming Hsieh

---

## Comment 1

> Username: jszuppe  
> Created at: 2018-12-30 12:28:51 UTC  
> Url: https://github.com/boostorg/compute/issues/795#issuecomment-450557567  

I don't have a Hawaii to reproduce the problem. Does it occur on any other configuration (new AMD GPU, NVIDIA GPU)?

---

## Comment 2

> Username: ymhsieh  
> Created at: 2019-01-02 07:03:35 UTC  
> Url: https://github.com/boostorg/compute/issues/795#issuecomment-450797074  

I've tested on NVIDIA GPUs and it is okay.  I do not have other AMD GPUs to test it out.  I'm willing to give you access to the Hawaii GPUs via ssh on my computers...

---

## Comment 3

> Username: rosenrodt  
> Created at: 2019-01-21 12:26:01 UTC  
> Url: https://github.com/boostorg/compute/issues/795#issuecomment-456057048  

@ymhsieh can you help verify if this issue still persists with the current develop branch?

---

## Comment 4

> Username: ymhsieh  
> Created at: 2019-01-22 06:44:35 UTC  
> Url: https://github.com/boostorg/compute/issues/795#issuecomment-456288602  

I just tried the "develop" branch and the problem appears to go away.... :-)   
Thank you ...
