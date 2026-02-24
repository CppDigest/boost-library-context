# #166 - posix child termination: use SIGTERM instead of SIGKILL [Closed]

> Username: joergreiling  
> Created at: 2020-07-29 19:43:05 UTC  
> Updated at: 2020-07-31 04:24:33 UTC  
> Closed at: 2020-07-30 04:57:01 UTC  
> Url: https://github.com/boostorg/process/issues/166  

Using signal 'SIGKILL' on posix for child termination shouldn't be the preferred way.  
  
You can of course install a signal-handler for 'SIGKILL', but this handler will never be called. So the child-process has no possibilty to react on a termination (closing descriptors, flushing caches, freeing resources...), a proper shutdown is not possible.   
  
Why don't we use signal 'SIGTERM' instead on Linux and Mac OS?   
  
That's also the way how it is done by Qt/QProcess.  
  
Feature-Request:  
- use 'SIGTERM' for child::terminate()  
- use 'SIGKILL' for new feature child::kill()

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2020-07-30 04:57:01 UTC  
> Url: https://github.com/boostorg/process/issues/166#issuecomment-666111734  

It is not the preferred way, it's the only way that has an equivalent on windows.   
There is no `SIGTERM` on windows that works for every process, hence there is no `terminate` in the posix sense in `boost.process` since it's a posix only feature. `TerminateProcess` is the windows version of `SIGKILL`.

---

## Comment 2

> Username: joergreiling  
> Created at: 2020-07-30 09:11:48 UTC  
> Url: https://github.com/boostorg/process/issues/166#issuecomment-666249042  

Graceful process-termination on Windows: **WM_CLOSE**

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2020-07-31 04:24:33 UTC  
> Url: https://github.com/boostorg/process/issues/166#issuecomment-666910214  

Only for win32 applications, not for console application. Quite different than on posix.
