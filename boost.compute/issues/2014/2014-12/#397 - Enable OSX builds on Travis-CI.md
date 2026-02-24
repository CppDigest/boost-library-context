# #397 - Enable OSX builds on Travis-CI [Closed]

> Username: kylelutz  
> Created at: 2014-12-30 21:59:12 UTC  
> Updated at: 2016-02-09 18:19:41 UTC  
> Closed at: 2016-02-09 18:19:38 UTC  
> Url: https://github.com/boostorg/compute/issues/397  

It looks like Travis-CI now supports building a project on both Linux and Mac OS X. We should enable this for Boost.Compute to increase the test coverage and catch OS X specific issues quickly. Some investigation may have to be done on running OpenCL effectively on Travis-CI with OS X.  
  
See http://blog.travis-ci.com/2014-05-13-multi-os-feature-available/ for a brief overview.

---

## Comment 1

> Username: roshanrags  
> Created at: 2014-12-30 23:00:39 UTC  
> Url: https://github.com/boostorg/compute/issues/397#issuecomment-68408330  

I contacted Travis's support a few days back about this and am about to get it enabled in my fork...

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-12-30 23:26:33 UTC  
> Url: https://github.com/boostorg/compute/issues/397#issuecomment-68410191  

Awesome! Thanks for working on this.

---

## Comment 3

> Username: kylelutz  
> Created at: 2015-01-21 04:08:52 UTC  
> Url: https://github.com/boostorg/compute/issues/397#issuecomment-70781665  

Implemented in PR #415.

---

## Comment 4

> Username: kylelutz  
> Created at: 2016-02-09 18:19:33 UTC  
> Url: https://github.com/boostorg/compute/issues/397#issuecomment-181990147  

Implemented in PR #554.
