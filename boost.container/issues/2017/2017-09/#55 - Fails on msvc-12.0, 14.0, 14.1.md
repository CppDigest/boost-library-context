# #55 - Fails on msvc-12.0, 14.0, 14.1 [Closed]

> Username: pdimov  
> Created at: 2017-09-07 17:27:55 UTC  
> Updated at: 2017-09-10 01:07:35 UTC  
> Closed at: 2017-09-10 00:44:57 UTC  
> Url: https://github.com/boostorg/container/issues/55  

See  
  
https://ci.appveyor.com/project/boostorg/boost/build/1.0.3616

---

## Comment 1

> Username: igaztanaga  
> Created at: 2017-09-07 18:18:43 UTC  
> Url: https://github.com/boostorg/container/issues/55#issuecomment-327882056  

Thanks Peter,  
  
It seems that I hadn't pushed an intrusive commit to boostorg. Let's see if this commit fixes the issue:  
  
https://github.com/boostorg/intrusive/commit/7f5caca12eac63ce3381493f3f8a861c35b03076

---

## Comment 2

> Username: pdimov  
> Created at: 2017-09-07 18:32:55 UTC  
> Url: https://github.com/boostorg/container/issues/55#issuecomment-327885937  

Do you need help setting up Travis/Appveyor for container and/or intrusive?

---

## Comment 3

> Username: igaztanaga  
> Created at: 2017-09-10 00:44:57 UTC  
> Url: https://github.com/boostorg/container/issues/55#issuecomment-328312244  

It seems it's fixed now:  
  
  https://ci.appveyor.com/project/boostorg/boost/build/1.0.3656-develop/job/cj0r7nhhpd4bv5ax#L6  
  
I should set up Travis/Appveyor for all my libraries. Are instructions at  
  
  https://svn.boost.org/trac10/wiki/TravisCoverals  
  
the way to go?

---

## Comment 4

> Username: pdimov  
> Created at: 2017-09-10 01:07:35 UTC  
> Url: https://github.com/boostorg/container/issues/55#issuecomment-328312923  

You could go with that, but I'd recommend you look at the .travis/appveyor.yml files in my libraries - Assert, Bind, Core, Mp11, SmartPtr, System - and lift the one you like best, edited appropriately. It'd be better to start with Appveyor, as Travis has problems at the moment (https://www.traviscistatus.com/) and has accumulated an enormous backlog. On Appveyor, you can enable the repos from your own account. On Travis, they go into the boostorg account, so I'll need to enable them for you once you have the .travis.yml files - just let me know.
