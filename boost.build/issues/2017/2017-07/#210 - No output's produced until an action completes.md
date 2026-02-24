# #210 - No output's produced until an action completes [Closed]

> Username: twesterhout  
> Created at: 2017-07-07 08:23:45 UTC  
> Updated at: 2018-01-26 19:51:38 UTC  
> Closed at: 2018-01-26 19:51:38 UTC  
> Url: https://github.com/boostorg/build/issues/210  

Currently, output of commands called in `actions` is buffered and not displayed until a command completes (either finished successfully or fails). See also this discussion on the boost-dev mailing list:  
http://boost.2283326.n4.nabble.com/build-No-output-until-an-action-completes-td4696541.html  
  
It would be nice to have an option to turn buffering off and see the output immediately.

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-01-23 15:43:37 UTC  
> Url: https://github.com/boostorg/build/issues/210#issuecomment-359831213  

Actually, the output is not displayed at all if the command succeeds unless you pass -d2.  
I was considering making early output the default for -j1, but obviously that would cause a change in behavior.
