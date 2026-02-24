# #454 - Optimize raw file copy action to coalesce into few copies. [Open]

> Username: grafikrobot  
> Created at: 2019-06-26 20:30:49 UTC  
> Updated at: 2021-05-29 16:23:05 UTC  
> Url: https://github.com/boostorg/build/issues/454  

Boost uses a bunch of individual file copy targets to install the headers files. This is rather slow since it ends up in thousands of copy command processes. Either adjust existing copy actions, and create new actions, for copying files in batches instead.

---

## Comment 1

> Username: lppinto  
> Created at: 2020-12-22 11:19:57 UTC  
> Url: https://github.com/boostorg/build/issues/454#issuecomment-749491004  

I just came here to report this... It's rather funny that in cygwin the process of copying each individual include file is way slower than the build itself... :D It's really frustrating :D

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:36 UTC  
> Url: https://github.com/boostorg/build/issues/454#issuecomment-850859631  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
