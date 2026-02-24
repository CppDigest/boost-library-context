# #92 - Portable Stacktrace [Closed]

> Username: igoloe  
> Created at: 2020-05-26 22:55:40 UTC  
> Updated at: 2024-09-12 08:19:39 UTC  
> Closed at: 2024-09-12 08:19:39 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/92  

Hi,  
is there any interest in making traces portable, so that symbolic resolution may be done later on another machine or environment with knowledge of binaries and debug symbols.  
At least for windows this can be done, but i am not sure, if the same mechanisms maybe used for linux to. Besides such a serialization wont be os independent.

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-05-28 13:51:01 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/92#issuecomment-635362870  

Sounds interesting, but I have to see the PR to be sure

---

## Comment 2

> Username: apolukhin  
> Created at: 2024-09-12 08:19:39 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/92#issuecomment-2345577919  

Closing as a dup of #180
