# #29 Add cmake build to travis [Closed]

> Username: seelabs  
> Created at: 2016-06-17 15:11:31 UTC  
> Updated at: 2017-07-25 17:44:16 UTC  
> Closed at: 2016-07-21 21:02:19 UTC  
> Url: https://github.com/boostorg/beast/pull/29  



---

## Comment 1

> Username: seelabs  
> Created_at: 2016-06-17 15:12:59 UTC  
> Url: https://github.com/boostorg/beast/pull/29#issuecomment-226796481  

@vinniefalco Please note I forced travis to use trusty (ubuntu 14.04) to get a later version of cmake. Is that change OK with you?

---

## Comment 2

> Username: codecov-io  
> Created_at: 2016-06-17 15:44:55 UTC  
> Updated_at: 2016-06-21 20:48:35 UTC  
> Url: https://github.com/boostorg/beast/pull/29#issuecomment-226805104  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/29?src=pr) is **98.10%**  
  
> Merging [#29](https://codecov.io/gh/vinniefalco/Beast/pull/29?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will not change coverage  
  
``` diff  
@@             master        #29   diff @@  
==========================================  
  Files            72         72            
  Lines          5073       5073            
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
  Hits           4977       4977            
  Misses           96         96            
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last updated by [999e2fa...5d22d70](https://codecov.io/gh/vinniefalco/Beast/compare/999e2fa0318b5982736d3ea01a418770ea802671...5d22d70e00e6a869439cb8f711e62a7ddb18ed66)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2016-06-17 19:45:24 UTC  
> Url: https://github.com/boostorg/beast/pull/29#issuecomment-226864502  

@seelabs Uhhh... I don't know? What are the pros and cons?

---

## Comment 4

> Username: seelabs  
> Created_at: 2016-06-17 20:03:28 UTC  
> Url: https://github.com/boostorg/beast/pull/29#issuecomment-226868232  

@vinniefalco   
  
Pros: We can use more recent packages, in this case I care about using a relatively modern cmake. Ubuntu 12.04 is four years old at this point.  
  
Cons: Not all travis features are supported in 14.04 (containers, for example). I don't think we care. I think we should go with 14.04.

---

## Comment 5

> Username: seelabs  
> Created_at: 2016-06-17 20:52:22 UTC  
> Url: https://github.com/boostorg/beast/pull/29#issuecomment-226878630  

@ximinez I'm making some changes to this PR (adding clang and installing a recent cmake on 12.04). Please hold off reviewing until that's done.

---

## Comment 6

> Username: seelabs  
> Created_at: 2016-06-20 11:59:42 UTC  
> Url: https://github.com/boostorg/beast/pull/29#issuecomment-227122959  

@ximinez This is ready to be reviewed. Two notes:   
  
1) The top two commits are from another PR and don't need to be reviewed there.  
2) I left in a hack where a cached directory was found, but the executables expected under that directory were not found (that shouldn't happen). If I figure out what's going on there I'll take out the hacks.

---

## Comment 7

> Username: ximinez  
> Created_at: 2016-06-20 21:37:31 UTC  
> Url: https://github.com/boostorg/beast/pull/29#issuecomment-227277474  

Looks good, and with a little coaxing, Travis succeeds. I'll note that it looks like the second commit overwrites the changes from the first commit (the ones I was supposed to ignore, but didn't). Those two commits should probably be squashed, or just remove the first one. :+1:

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2016-07-21 20:56:36 UTC  
> Url: https://github.com/boostorg/beast/pull/29#issuecomment-234381551  

Part of #39

---
