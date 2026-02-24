# #336 Add VxWorks process extension. [Closed]

> Username: kuhlenough  
> Created at: 2018-08-28 20:39:51 UTC  
> Updated at: 2021-10-02 21:19:08 UTC  
> Closed at: 2019-03-29 15:44:52 UTC  
> Url: https://github.com/boostorg/build/pull/336  

We use "vxe" extension for VxWorks processes by convention, no tool requires it, its just an easy way to identify the cross built executable.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2018-10-12 00:35:28 UTC  
> Url: https://github.com/boostorg/build/pull/336#issuecomment-429165893  

1. I'm not sure about this change in that it changes everyone's extension on vxworks. Perhaps they are happy without an extension.  
2. The tests fail because they expect the sans-extension executable in this case. You would need to adjust the test to compensate for the new behavior.

---

## Comment 2

> Username: kuhlenough  
> Created_at: 2018-10-12 14:46:23 UTC  
> Url: https://github.com/boostorg/build/pull/336#issuecomment-429350972  

Where are these sanity tests stored, perhaps I can contribute some updates to make them a little more realistic?     
BTW.  If I had a kernel  on QEMU could Travis or AppVeyor, start it so we could do some run testing?

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2018-10-12 23:17:59 UTC  
> Url: https://github.com/boostorg/build/pull/336#issuecomment-429487415  

The test that's failing is this one https://github.com/boostorg/build/blob/develop/test/toolset_clang_vxworks.py  
  
Which derives from this https://github.com/boostorg/build/blob/develop/test/TestToolset.py

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2018-10-12 23:22:40 UTC  
> Url: https://github.com/boostorg/build/pull/336#issuecomment-429488068  

Not sure about QEMU. Haven't investigated that possibility. But it would be really nice to do that :-) So I'll look into it. And if you can direct me to some documentation or hints on how to do that I would appreciate it.

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2018-10-12 23:31:28 UTC  
> Url: https://github.com/boostorg/build/pull/336#issuecomment-429489244  

@kuhlenough if you have some comments about QEMU please add them to this issue https://github.com/boostorg/build/issues/355

---
