# #98 - No longer necessary to link against Boost system [Closed]

> Username: ton  
> Created at: 2019-01-14 15:45:30 UTC  
> Updated at: 2019-01-14 17:10:05 UTC  
> Closed at: 2019-01-14 17:09:49 UTC  
> Url: https://github.com/boostorg/filesystem/issues/98  

Judging from the build file and my very limited knowledge of Boost build files, the filesystem library still links against Boost system, even though it is now a header-only library since Boost 1.69.0.  
  
Please remove the dependency on Boost system, so that client code that links against Boost filesystem does not have to pull in the stub Boost system library. I am not 100% sure on the correct way to do it, otherwise I would have filed a pull request.

---

## Comment 1

> Username: Lastique  
> Created at: 2019-01-14 17:10:05 UTC  
> Url: https://github.com/boostorg/filesystem/issues/98#issuecomment-454083323  

Done, thanks.
