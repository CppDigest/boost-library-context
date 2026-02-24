# #56 - The appveyor scripts do not support building in a fork [Closed]

> Username: jeking3  
> Created at: 2017-09-27 16:16:59 UTC  
> Updated at: 2018-08-30 01:59:07 UTC  
> Closed at: 2018-08-29 03:34:34 UTC  
> Url: https://github.com/boostorg/predef/issues/56  

I pushed some changes into my fork and enabled appveyor for my fork, however all of the build jobs failed:  
  
https://ci.appveyor.com/project/jeking3/predef/build/1.0.1  
  
It looks like things are hardcoded to the original repo.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-05-13 13:04:10 UTC  
> Url: https://github.com/boostorg/predef/issues/56#issuecomment-388625364  

@grafikrobot this is a pretty serious issue.  I cannot preflight anything for predef in my own fork because the build scripts want to use boostorg.

---

## Comment 2

> Username: grafikrobot  
> Created at: 2018-05-13 15:17:09 UTC  
> Url: https://github.com/boostorg/predef/issues/56#issuecomment-388634403  

I'll see if I can do a quick fix tonight.

---

## Comment 3

> Username: jeking3  
> Created at: 2018-05-13 17:46:59 UTC  
> Url: https://github.com/boostorg/predef/issues/56#issuecomment-388643894  

I don't think a fix will be that simple since this repository relies on `https://raw.githubusercontent.com/boostorg/regression/develop/ci/src/ci_*` which assumes boostorg.  If we could pass the slug into those scripts it would help.    
  
Alternatively we could leverage the ci environment and scripts from uuid/format/date_time to add valgrind, ubsan, codecov, and covscan support, as long as we keep the same jobs that you have since you want the coverage for as many preprocessor branches as possible.  I'l doing this conversion on boostorg/pool right now... let me know if you want me to apply the same here.  It will eliminate any external CI dependencies (which is good and bad; right now I have 4 or 5 copies of my ci scripts and when I have to update one, it makes for many PRs!)

---

## Comment 4

> Username: grafikrobot  
> Created at: 2018-08-29 03:34:34 UTC  
> Url: https://github.com/boostorg/predef/issues/56#issuecomment-416812959  

After some rework to remove the boostorg dependencies the ci works standalone.

---

## Comment 5

> Username: jeking3  
> Created at: 2018-08-29 04:02:48 UTC  
> Url: https://github.com/boostorg/predef/issues/56#issuecomment-416816926  

I wonder if https://github.com/boostorg/predef/issues/59 is also addressed then?

---

## Comment 6

> Username: grafikrobot  
> Created at: 2018-08-30 01:59:06 UTC  
> Url: https://github.com/boostorg/predef/issues/56#issuecomment-417164360  

It doesn't. I'll add something to accommodate  arbitrary args.
