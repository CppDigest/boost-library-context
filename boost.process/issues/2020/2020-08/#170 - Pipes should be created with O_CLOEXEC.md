# #170 - Pipes should be created with O_CLOEXEC [Open]

> Username: Boronak  
> Created at: 2020-08-23 12:46:03 UTC  
> Updated at: 2020-08-24 14:48:35 UTC  
> Url: https://github.com/boostorg/process/issues/170  

On Linux all file handles are inherited by default by child processes. This means that when you create an `opstream` and attach it to a child process, the child process with inherit the input side of the pipe. When you try to close the pipe from the parent process, the operating system will still consider the input of the pipe to be open in the child and the process will most likely block indefinitely. This can be worked around by passing a `boost::process::pipe` created from the result of `pipe2(pipefd, O_CLOEXEC)`, however it would be better to have this behaviour by default. Please note that this must be done at the initial `pipe` call and not with `fcntl` later as otherwise there would be a race condition in multithreaded code.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2020-08-24 13:37:55 UTC  
> Url: https://github.com/boostorg/process/issues/170#issuecomment-679131282  

Wouldn't that close every pipe, including those I want to pass to the child process? I mean, it works if you just want to use `stdio` , but if you want to add another pipe this will not work at all.  
Plus, we have [`process::limit_handles` ](https://www.boost.org/doc/libs/1_74_0/doc/html/process/reference.html#header.boost.process.handles_hpp) for that.

---

## Comment 2

> Username: Boronak  
> Created at: 2020-08-24 14:18:52 UTC  
> Url: https://github.com/boostorg/process/issues/170#issuecomment-679155563  

I am not familiar with `limit_handles` I will look into it.  
`O_CLOEXEC` only closes on exec. When you spawn a child process you first call `fork` and the handles are inherited by the child process. They are duplicated to `stdin` and `stdout` with `dup2`. This copies the handles. Now `stdin` and `stdout` are the correct handles, and are not `O_CLOEXEC`. Then `exec` is called and only then are the old `O_CLOEXEC` handles closed. You could close the handles manually but the problem is that if the code is multithreaded any random process spawned by another thread for any reason can "steal" your file handles and then there is no way to close them.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2020-08-24 14:33:45 UTC  
> Url: https://github.com/boostorg/process/issues/170#issuecomment-679163929  

`stdin` and `stdout` may or may not be the correct handles. They probably are for 99% of the cases, but I cannot assume that there is no legitimate use for additional ones. This library actually provides `posix::fd.bind` for that.

---

## Comment 4

> Username: Boronak  
> Created at: 2020-08-24 14:48:35 UTC  
> Url: https://github.com/boostorg/process/issues/170#issuecomment-679172324  

By "correct handles" I meant code like `::dup2(handle, STDIN_FILENO)` which changes the STDIN/STDOUT file handles to be copies of the pipe ones. When you attach a pipe to a child process it doesn't actually uses the pipe handles. It dups them into it's STDIN/STDOUT.
