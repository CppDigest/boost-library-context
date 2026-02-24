# #283 - Add async_pipe constructor taking native_handles [Open]

> Username: reddwarf69  
> Created at: 2022-12-13 13:11:19 UTC  
> Updated at: 2022-12-13 13:11:19 UTC  
> Url: https://github.com/boostorg/process/issues/283  

async_pipe has native_sink() and native_source() methods, returning native_handles, which can be used to do things like `fcntl(pipe.native_sink(), F_SETFD, FD_CLOEXEC)`.  
But, to avoid race conditions, it would be better to directly create the pipe with O_CLOEXEC. I doubt the CLOEXEC concept is portable but, since the interface already exposes native_handles, maybe a constructor could be added taking native_handles? This way people could create the pipe themselves, and then pass it to async_pipe.  
  
I would suggest defaulting to O_CLOEXEC, but that would be a breaking change.
