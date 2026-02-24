# #285 - Zombie process created [Open]

> Username: reddwarf69  
> Created at: 2022-12-15 13:17:05 UTC  
> Updated at: 2023-07-02 19:09:57 UTC  
> Url: https://github.com/boostorg/process/issues/285  

In https://github.com/boostorg/process/commit/bbabea30dd0940ab604e332a50b38013138dd038 the handling of execve() errors was supposedly fixed.  
But if I run  
```  
#include <boost/process.hpp>  
#include <unistd.h>  
  
int main() {  
	try {  
	 	boost::process::child("doesnt-exist");  
	}catch(...){}  
  
	sleep(100);  
}  
```  
via strace, I can't see any wait() call  
 ```  
$ strace -f -e process ./test  
execve("./test", ["./test"], 0x7ffe241129e8 /* 11 vars */) = 0  
clone(child_stack=NULL, flags=CLONE_CHILD_CLEARTID|CLONE_CHILD_SETTID|SIGCHLD, child_tidptr=0x7ff79b6ca6d0) = 84374  
strace: Process 84374 attached  
[pid 84374] execve("doesnt-exist", ["doesnt-exist"], 0x7ffd4520dd58 /* 11 vars */) = -1 ENOENT (No such file or directory)  
[pid 84374] exit_group(1)               = ?  
[pid 84374] +++ exited with 1 +++  
--- SIGCHLD {si_signo=SIGCHLD, si_code=CLD_EXITED, si_pid=84374, si_uid=1000, si_status=1, si_utime=0, si_stime=0} ---  
```  
And running top (and pressing `V` to get a process tree) while the program is in the `sleep(100);` confirms there is a zombie process.  
  
I'm using Boost 1.81.0.  
  
By the way, in https://github.com/boostorg/process/blob/a9f083f45c6da4273885d5869c1a55c1c7d19c7a/test/error.cpp#L113 and https://github.com/boostorg/process/blob/a9f083f45c6da4273885d5869c1a55c1c7d19c7a/test/error.cpp#L122, "doesnt-exit" probably is supposed to be "doesnt-exist"?

---

## Comment 1

> Username: reddwarf69  
> Created at: 2022-12-15 14:11:16 UTC  
> Url: https://github.com/boostorg/process/issues/285#issuecomment-1353154783  

FWIW the waitpid in https://github.com/boostorg/process/blob/boost-1.81.0/include/boost/process/detail/posix/executor.hpp#L448 is never executed because https://github.com/boostorg/process/blob/boost-1.81.0/include/boost/process/detail/posix/executor.hpp#L442 throws an exception (https://github.com/boostorg/process/blob/boost-1.81.0/include/boost/process/detail/posix/executor.hpp#L180).

---

## Comment 2

> Username: SilverPlate3  
> Created at: 2023-07-02 19:09:56 UTC  
> Url: https://github.com/boostorg/process/issues/285#issuecomment-1616776868  

@klemens-morgenstern   
Issue is fixed and tested in pull request https://github.com/boostorg/process/pull/325  
I think issue can be closed.
