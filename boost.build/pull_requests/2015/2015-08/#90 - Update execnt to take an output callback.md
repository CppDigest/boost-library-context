# #90 Update execnt to take an output callback. [Closed]

> Username: frenchtoast747  
> Created at: 2015-08-15 23:33:16 UTC  
> Updated at: 2021-10-02 22:20:20 UTC  
> Closed at: 2015-08-19 08:25:45 UTC  
> Url: https://github.com/boostorg/build/pull/90  

This worked with my super simple project in Windows.. I think the usage of the `ioBuffer` should be fine since it looks like Python is going to create a copy of the buffer in the call to `PyString_FromStringAndSize` inside of `make1c_output_closure`. I haven't gotten the client WebSocket demo to work 100% yet, but what I have here should be a good start. Just thought I'd share in case anyone else wants to do some work on Windows.

---

## Comment 1

> Username: frenchtoast747  
> Created_at: 2015-08-17 16:26:25 UTC  
> Url: https://github.com/boostorg/build/pull/90#issuecomment-131879382  

I should mention that this is to enable support for JSON output in Windows on the server branch.

---

## Comment 2

> Username: vprus  
> Created_at: 2015-08-18 06:23:42 UTC  
> Url: https://github.com/boostorg/build/pull/90#issuecomment-132087296  

For avoidance of doubt, which Python version was you testing with - I want to install the same to avoid incidental issues.

---

## Comment 3

> Username: frenchtoast747  
> Created_at: 2015-08-18 21:37:34 UTC  
> Url: https://github.com/boostorg/build/pull/90#issuecomment-132361723  

Python 2.7.8 (default, Jun 30 2014, 16:03:49) [MSC v.1500 32 bit (Intel)] on win32

---

## Comment 4

> Username: vprus  
> Created_at: 2015-08-19 08:25:45 UTC  
> Url: https://github.com/boostorg/build/pull/90#issuecomment-132490292  

Cherry-picked, thanks!  
  
VS14 issued some warnings:  
  
```  
execnt.c(747): warning C4047: 'function': 'int' differs in levels of indirection from 'HANDLE'  
execnt.c(747): warning C4024: 'output_func': different types for formal and actual parameter 2  
```  
  
Do we care? It also seems that 'develop' has drifted a bit, I'll probably try a merge from develop to server in some future.

---

## Comment 5

> Username: frenchtoast747  
> Created_at: 2015-08-19 13:21:12 UTC  
> Url: https://github.com/boostorg/build/pull/90#issuecomment-132592910  

I've never used the Windows API before, but from what I can tell, a `HANDLE` type is just an abstract type used for streams, file descriptors, etc. Since the `stream` output is just the ID of the stream, it could probably be cast to an `int` and still maintain meaning while squelching the warning.  
  
Thanks!

---

## Comment 6

> Username: vprus  
> Created_at: 2015-09-03 08:27:24 UTC  
> Url: https://github.com/boostorg/build/pull/90#issuecomment-137377649  

I've looked into that warning again. Right now, it becomes a 'stream' field in JSON output, which we don't use at all. In theory, we might want to distinguish between stdout and stderr, and on Linux, that would be easy to check stream field - it will be either 1 or 2. With the current Windows code that will fail, since HANDLE is usually something very well above zero, and not predictable. So, we'll eventually need to either remove 'stream' parameter, or make conversion from HANDLE to int more smart. But it's not very important right now.

---

## Comment 7

> Username: frenchtoast747  
> Created_at: 2015-09-03 18:29:10 UTC  
> Url: https://github.com/boostorg/build/pull/90#issuecomment-137535660  

Alright. I will keep that in mind. Thanks!

---
