# #228 VxWorks support for b2  [Merged]

> Username: kuhlenough  
> Created at: 2017-08-16 16:37:12 UTC  
> Updated at: 2021-10-02 22:08:36 UTC  
> Merged at: 2017-09-02 15:30:21 UTC  
> Closed at: 2017-09-02 15:30:21 UTC  
> Url: https://github.com/boostorg/build/pull/228  

While the source contains VxWorks specific defines and configuration, the process of the building in a cross-build environment has been hackneyed.   
For example:    
https://svn.boost.org/trac10/wiki/Guidelines/VxWorks  
  
This is less than ideal, as VxWorks of any vintage supports at minimum 3 processor families and utilizes multiple compilers that vary by specific processor. So any given developer must adapt any instructions to there environment.     
  
This pull adds the ability to use b2 with VxWorks, and and more importantly begins the path to having each VxWorks developer being able to run test suite against there particular BSP (board support package.)  
  
Additional pulls  to add supporting changes to config, test, and individual libraries will follow.  
Current test results, supporting configuration examples, and instructions found here:  
https://github.com/Wind-River/vxworks7-layer-for-boost

---

## Comment 1

> Username: kuhlenough  
> Created_at: 2017-08-28 21:22:55 UTC  
> Url: https://github.com/boostorg/build/pull/228#issuecomment-325486147  

I see this pull now has conflict.    
Is there a reason it has not been merged previously?  
Would you like the changes in another format?

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2017-08-31 15:46:28 UTC  
> Url: https://github.com/boostorg/build/pull/228#issuecomment-326337547  

Sorry about that.. I had started I cleanup of gcc.jam before your PR came up. And I was waiting on the 1.65 release to merge that in. I can try and apply your changes to the new code. But I would be somewhat guessing as to the specifics since I wouldn't be able to test it. So if you could resolve the conflicts and update the PR that would be awesome.

---

## Comment 3

> Username: kuhlenough  
> Created_at: 2017-09-02 03:20:38 UTC  
> Url: https://github.com/boostorg/build/pull/228#issuecomment-326718690  

fixed conflicts

---
