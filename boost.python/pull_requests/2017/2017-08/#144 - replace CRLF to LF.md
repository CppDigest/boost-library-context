# #144 replace CRLF to LF [Open]

> Username: joelin  
> Created at: 2017-08-04 08:11:28 UTC  
> Updated at: 2017-08-05 04:07:43 UTC  
> Url: https://github.com/boostorg/python/pull/144  

i found this error when i checkout branch svn-branches/system at my linux computer.   
`modified:   build/VisualStudio/boost_python.dsp`   
the reason is CRLF.   
I synchronizing the all source repo(include all branch and tags ) to intranet and then found this error, it breaks the  job of  synchronize. so replace it to LF.

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2017-08-04 12:24:50 UTC  
> Url: https://github.com/boostorg/python/pull/144#issuecomment-320235579  

That's an unmaintained branch, and I'm tempted to remove it (as well as many other similar ones). Can't you limit the branches to sync to, say, "master" and "develop" ?

---

## Comment 2

> Username: joelin  
> Created_at: 2017-08-05 04:07:43 UTC  
> Url: https://github.com/boostorg/python/pull/144#issuecomment-320394743  

@stefanseefeld thx! I synchronized all branches because i don't know which one is need by another module, eg. ceph. i can limit the branches via customized sync job. it's going to be complicated, that's not what I want. so the best way is delete it or modify it corrected.

---
