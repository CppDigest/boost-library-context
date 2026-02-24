# #621 - Investigate warning user that Json is not going to be built when explicitly requested [Closed]

> Username: grisumbras  
> Created at: 2021-09-30 16:48:38 UTC  
> Updated at: 2021-10-06 11:03:07 UTC  
> Closed at: 2021-10-06 11:03:07 UTC  
> Url: https://github.com/boostorg/json/issues/621  

If the user asks for Json via `b2 --with-json` but building it is skipped due to unmet requirements (pre-11 compiler) the build is skipped silently. It should be possible to warn the user that the library won't be built.
