# #92 - A bench test appears to lock up in Visual Studio 2015 64-bit release mode, default /std [Closed]

> Username: jeking3  
> Created at: 2018-11-14 02:41:52 UTC  
> Updated at: 2021-04-22 14:10:33 UTC  
> Closed at: 2021-04-22 14:10:33 UTC  
> Url: https://github.com/boostorg/container/issues/92  

Build job:  
  
https://ci.appveyor.com/project/jeking3/container/builds/20276121/job/3fj1m0948n8bamon  
  
It runs for an hour and times out.  This has happened on two different builds, so I think it's specific to Visual Studio 2015.  I added the bench directory to the top level Jamfile in a preflight CI pull request in my fork.  Given this has happened twice, I thought I would report it.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-04-22 14:10:33 UTC  
> Url: https://github.com/boostorg/container/issues/92#issuecomment-824876286  

Closing this old bug since it's been difficult to diagnose and it doesn't seem to have impacted users.
