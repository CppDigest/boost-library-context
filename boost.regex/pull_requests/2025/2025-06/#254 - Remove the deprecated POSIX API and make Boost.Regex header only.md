# #254 Remove the deprecated POSIX API and make Boost.Regex header only. [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2025-06-29 10:04:28 UTC  
> Updated at: 2025-07-04 19:40:31 UTC  
> Closed at: 2025-07-04 19:40:31 UTC  
> Url: https://github.com/boostorg/regex/pull/254  

Closes #253

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2025-06-29 10:12:13 UTC  
> Url: https://github.com/boostorg/regex/pull/254#issuecomment-3016537460  

I realise it's not used much, but what exactly do we gain from this?

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created_at: 2025-06-29 10:26:09 UTC  
> Url: https://github.com/boostorg/regex/pull/254#issuecomment-3016566017  

This pull request is not working at all at the moment (I still have to learn how all these Jamfile things work).  
  
Technically, what I am trying to fix on my side is the fact that following the modular work, other boost libraries now have an explicit dependency on libboost_regex.so, while they used not to. For example libboost_iostreams.so depends again on libboost_regex.so now (in Boost 1.88):  
  
```  
rgeissler@ncerndobedev6097:~/wk/tracer/inputlogger (release/25.0.0 $ u+1)> readelf -d test/links/x86_64-v25/Debug/libboost_iostreams.so|grep NEEDED|grep libboost                                             10:16AM  
 0x0000000000000001 (NEEDED)             Shared library: [libboost_regex.so.1.88.0]  
 0x0000000000000001 (NEEDED)             Shared library: [libboost_random.so.1.88.0]  
```  
  
while it wasn't the case in earlier versions of Boost (for example 1.83 that I used before):  
  
```  
rgeissler@ncerndobedev6097:~/wk/tracer/inputlogger (release/25.0.0 $ u+1)> readelf -d /data/mwrep/res/osp/Boost/iostreams/23-0-0-10/lib/x86_64-v23/Debug/libboost_iostreams.so.1.83.0|grep NEEDED|grep libboost  
(no output)  
```  
  
It seems Boost.Iostreams doesn't need the posix API of Boost.regex, nor the random_device from Boost.random, so it has no reason to depend on the corresponding shared libraries. This is what I wish to fix. Maybe the approach here is wrong, and we shall instead find a way to link Boost.Iostreams without explicit link dependencies on Boost.Regex/Random

---

## Comment 3

> Username: Romain-Geissler-1A  
> Created_at: 2025-06-29 10:27:47 UTC  
> Url: https://github.com/boostorg/regex/pull/254#issuecomment-3016568860  

See a similar symptom that happened in Boost.filesystem because of the header-only Boost.System in https://github.com/boostorg/filesystem/issues/336 (this seems to be a global consequence in Boost with the modularization work).

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2025-06-30 15:34:33 UTC  
> Url: https://github.com/boostorg/regex/pull/254#issuecomment-3019653644  

It's the wrong fix, but you are correct that the issue has become pervasive post-modular build and that there are a lot of greedy and unnecessary dependencies creeping in (I've been hit with this as well when testing Boost.Math).  
  
Let me think about this.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2025-07-04 19:40:31 UTC  
> Url: https://github.com/boostorg/regex/pull/254#issuecomment-3037137685  

This should have been addressed by https://github.com/boostorg/regex/pull/256

---
