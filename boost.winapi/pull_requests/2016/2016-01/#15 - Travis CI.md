# #15 Travis CI [Closed]

> Username: klemens-morgenstern  
> Created at: 2016-01-27 12:02:47 UTC  
> Updated at: 2016-05-24 09:19:38 UTC  
> Closed at: 2016-05-24 09:19:38 UTC  
> Url: https://github.com/boostorg/winapi/pull/15  

Ok, so this Pull requests just contains the two Travis CI files, including a short addition to the readme. If you don't want that, just pull 87999ec.   
  
You can find the current build here: https://travis-ci.org/MorgWal/winapi

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2016-01-27 18:15:10 UTC  
> Url: https://github.com/boostorg/winapi/pull/15#issuecomment-175778414  

Oh, to use it, you just need to activate Travis CI as webservice in the github-repo settings and then login to https://travis-ci.org to activate the repository there.

---

## Comment 2

> Username: Lastique  
> Created_at: 2016-01-27 19:01:18 UTC  
> Url: https://github.com/boostorg/winapi/pull/15#issuecomment-175795198  

This will be problematic as I don't have the admin rights for the GitHub project. In any case, it'll take time as I need to understand how it works.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2016-01-27 19:21:41 UTC  
> Url: https://github.com/boostorg/winapi/pull/15#issuecomment-175808978  

Oh ok. If you try this yml file you'll get a problem if you use another branch than develop or master. That is because it checks boostorg/boost out with the same branch.  
  
The travis.jam only exists so it can be copied to user-config.jam which has the toolset-config. That could also be integrated into .travis.yml, but I don't think travis.jam will cause a problem.

---

## Comment 4

> Username: apolukhin  
> Created_at: 2016-01-29 18:20:26 UTC  
> Url: https://github.com/boostorg/winapi/pull/15#issuecomment-176894959  

There's a recipe on how to enable TravisCI + Coveralls without admin rights: https://svn.boost.org/trac/boost/wiki/TravisCoverals

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2016-05-23 10:56:06 UTC  
> Url: https://github.com/boostorg/winapi/pull/15#issuecomment-220949156  

Hey Andrey,  
  
do you now have admin rights? Will this PR be pulled anytime soon, or can I close it?

---

## Comment 6

> Username: Lastique  
> Created_at: 2016-05-23 11:39:50 UTC  
> Url: https://github.com/boostorg/winapi/pull/15#issuecomment-220957134  

I don't have the admin rights. Not yet, anyway.  
  
I had some time to look into Travis CI and Appveyor, and I'll probably go with the latter when I have more time. After all, the main target for Boost.WinAPI is Windows and MSVC, which Travis CI do not support.

---

## Comment 7

> Username: klemens-morgenstern  
> Created_at: 2016-05-23 11:45:12 UTC  
> Url: https://github.com/boostorg/winapi/pull/15#issuecomment-220958080  

Makes sense, I didn't know appveyor when I created this PR. Though you could still use travis to also build with mingw.

---

## Comment 8

> Username: klemens-morgenstern  
> Created_at: 2016-05-24 09:19:38 UTC  
> Url: https://github.com/boostorg/winapi/pull/15#issuecomment-221212883  

Ok, I added an appveyor.yml, but didn't test it, because it's in my organisation. So you could maybe just copy it.

---
