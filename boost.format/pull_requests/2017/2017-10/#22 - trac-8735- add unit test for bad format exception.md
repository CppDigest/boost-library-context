# #22 trac-8735: add unit test for bad format exception [Merged]

> Username: jeking3  
> Created at: 2017-10-12 19:54:52 UTC  
> Updated at: 2017-10-18 19:29:56 UTC  
> Merged at: 2017-10-13 14:06:09 UTC  
> Closed at: 2017-10-13 14:06:09 UTC  
> Url: https://github.com/boostorg/format/pull/22  

I built this in my fork which can do CI builds:  
  
Fork PR: https://github.com/jeking3/format/pull/5  
Appveyor: https://ci.appveyor.com/project/jeking3/format/build/1.0.9-develop  
Travis: https://travis-ci.org/jeking3/format/builds/287137260

---

## Comment 1

> Username: jeking3  
> Created_at: 2017-10-12 19:58:34 UTC  
> Updated_at: 2017-10-12 20:22:37 UTC  
> Url: https://github.com/boostorg/format/pull/22#issuecomment-336248779  

I moved this into its own file and check for the other (too few, too many argument) exceptions as well, the CI build above does not reflect this.

---
