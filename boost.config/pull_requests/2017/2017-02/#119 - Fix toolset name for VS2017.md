# #119 Fix toolset name for VS2017 [Closed]

> Username: KindDragon  
> Created at: 2017-02-20 18:29:45 UTC  
> Updated at: 2017-03-13 18:59:53 UTC  
> Closed at: 2017-03-13 18:59:52 UTC  
> Url: https://github.com/boostorg/config/pull/119  

I think we should change VS2017 toolset name to vc141 before next Boost release.  
  
Related to https://github.com/boostorg/build/pull/167  
  
/cc @AndrewPardoe

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-02-20 18:50:18 UTC  
> Url: https://github.com/boostorg/config/pull/119#issuecomment-281155829  

Some comments/questions:  
  
1) Boost.Build needs to change this first Config just matches whatever lib names are produced by BB.  
2) Boost.Build needs to support the next visual studio release period before this means much of anything anyway.  
3) Do we have official confirmation of what MS are calling this release?  What's the _MSC_VER version?  
  
Thanks.

---

## Comment 2

> Username: KindDragon  
> Created_at: 2017-02-20 23:08:58 UTC  
> Updated_at: 2017-02-20 23:09:37 UTC  
> Url: https://github.com/boostorg/config/pull/119#issuecomment-281202746  

> Boost.Build needs to change this first Config just matches whatever lib names are produced by BB.  
  
PR  boostorg/build#167 should fix this  
  
> Boost.Build needs to support the next visual studio release period before this means much of anything anyway.  
  
Now BB supports VS2017 if VS is installed to default path or you run Boost Build from "Developer Command Prompt for VS 2017".  
  
> Do we have official confirmation of what MS are calling this release? What's the _MSC_VER version?  
  
It should be VS2017 and _MSC_VER=1910. I think @AndrewPardoe can help us verify this.

---

## Comment 3

> Username: AndrewPardoe  
> Created_at: 2017-02-21 22:18:26 UTC  
> Url: https://github.com/boostorg/config/pull/119#issuecomment-281500274  

VS2017 contains the VC 14.1 toolset, or vc141. Part of the confusion is that the compiler's internal version, 19.10, is five higher than the public version, 14.1. So _MSC_VER=1910, but the toolset is vc141.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2017-03-13 18:59:52 UTC  
> Url: https://github.com/boostorg/config/pull/119#issuecomment-286209220  

This is now addressed in develop.

---
