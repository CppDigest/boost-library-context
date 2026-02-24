# #72 - travis fails on apple xcode [Open]

> Username: madebr  
> Created at: 2019-01-05 03:15:23 UTC  
> Updated at: 2019-01-05 03:33:36 UTC  
> Url: https://github.com/boostorg/program_options/issues/72  

I've forked this repo and [enabled macos on travis](https://github.com/madebr/program_options/commit/200e15075218680e185d94417e70d5f8be51fb30).  
  
The tests fail on macos: https://travis-ci.com/madebr/program_options/builds/96364270

---

## Comment 1

> Username: madebr  
> Created at: 2019-01-05 03:33:36 UTC  
> Url: https://github.com/boostorg/program_options/issues/72#issuecomment-451624137  

I believe this issue is related to a problem I have in my project.  
Travis log here: https://travis-ci.org/madebr/openrw/jobs/475165464#L3042  
  
I need to explicitly add `catch(boost::exception &)` to catch these exceptions on mac:  
https://github.com/madebr/openrw/blob/py2/rwgame/RWConfig.cpp#L112-L128
