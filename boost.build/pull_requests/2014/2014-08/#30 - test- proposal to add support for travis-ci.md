# #30 test: proposal to add support for travis-ci [Closed]

> Username: tee3  
> Created at: 2014-08-30 22:00:17 UTC  
> Updated at: 2021-10-02 22:35:48 UTC  
> Closed at: 2014-08-30 23:32:54 UTC  
> Url: https://github.com/boostorg/build/pull/30  

This adds support for [travis-ci](https://travis-ci.org) for Boost.Build.  It makes a few minor fixes in the test infrastructure as well as adds a Makefile to control the process.  This could use a lot more work to support multiple toolsets and platforms, but I thought I would propose it and see if it seems interesting.  
  
This is currently building from a [fork](https://github.com/tee3/build) of Boost.Build.  To enable it for the main repository, you would just have to add a key to the main repository and turn it on.  
  
You can see the results [here](https://travis-ci.org/tee3/build).  It is currently failing one test, I'm not sure why.

---

## Comment 1

> Username: tee3  
> Created_at: 2014-08-30 23:32:54 UTC  
> Url: https://github.com/boostorg/build/pull/30#issuecomment-53973647  

This was meant for my own experimentation, sorry about that :)  I doubt travis answers the needs of a Boost.Build test systems since Boost.Build runs on so many platforms and toolsets.

---
