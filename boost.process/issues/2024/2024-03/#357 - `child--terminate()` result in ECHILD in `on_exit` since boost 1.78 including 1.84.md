# #357 - `child::terminate()` result in ECHILD in `on_exit` since boost 1.78 including 1.84 [Open]

> Username: xbreak  
> Created at: 2024-03-13 12:50:55 UTC  
> Updated at: 2024-03-13 12:50:55 UTC  
> Url: https://github.com/boostorg/process/issues/357  

## Description  
  
In boost 1.77 the simple program below results in  
  
> Child completed with exit code=9, and error code: Success  
  
whereas since boost 1.78 (also tested 1.79, 1.80, 1.83 & 1.84) it results in  
  
> Child completed with exit code=-1, and error code: No child processes  
  
Only boost was changed for these tests compiled using gcc 13.2.0, but has been also been confirmed on other Linux systems with kernel version 5.15 and 6.2.9.  
  
## Test program  
  
```c++  
#include <boost/process.hpp>  
#include <boost/asio/io_context.hpp>  
  
#include <iostream>  
  
int main() try {  
  boost::asio::io_context ctx;  
  namespace bp = boost::process;  
  auto proc = bp::child(  
      bp::argv({"sleep", "5"}),  
      ctx, bp::on_exit = [](int exit, const std::error_code &ec) {  
      std::cerr << "Child completed with exit code="  
                << exit  
                << ", and error code: " << ec.message() << '\n';  
      });  
  
  proc.terminate();  
  
  // Run to completion  
  ctx.run();  
  
} catch (std::exception const &e) {  
  std::cerr << "Unexpected exception " << e.what() << '\n';  
}  
```  
  
## Syscalls  
  
If it help I'm including the resulting syscalls for both cases.  
  
In boost 1.78+ it seems to `waitpid()/wait4` a second time. Section extracted here:  
  
```  
...  
[pid 1387918] wait4(1387919,  <unfinished ...>  
[pid 1387919] +++ killed by SIGKILL +++  
<... wait4 resumed>[{WIFSIGNALED(s) && WTERMSIG(s) == SIGKILL}], 0, NULL) = 1387919  
...  
wait4(1387919, 0x7ffe02136250, WNOHANG, NULL) = -1 ECHILD (No child processes)  
```  
  
### boost 1.77 -> OK  
  
<details>  
  
```console  
$ strace -f -e '!%file,%memory' result/bin/test   
arch_prctl(0x3001 /* ARCH_??? */, 0x7ffc5bd058b0) = -1 EINVAL (Invalid argument)  
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\0\0\0\0\0\0\0\0"..., 832) = 832  
close(3)                                = 0  
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\0\0\0\0\0\0\0\0"..., 832) = 832  
close(3)                                = 0  
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\0\0\0\0\0\0\0\0"..., 832) = 832  
close(3)                                = 0  
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\0\0\0\0\0\0\0\0"..., 832) = 832  
close(3)                                = 0  
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\220\202\2\0\0\0\0\0"..., 832) = 832  
pread64(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784, 64) = 784  
pread64(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784, 64) = 784  
close(3)                                = 0  
arch_prctl(ARCH_SET_FS, 0x7f225d6c6740) = 0  
set_tid_address(0x7f225d6c6a10)         = 1388959  
set_robust_list(0x7f225d6c6a20, 24)     = 0  
rseq(0x7f225d6c7060, 0x20, 0, 0x53053053) = 0  
prlimit64(0, RLIMIT_STACK, NULL, {rlim_cur=8192*1024, rlim_max=RLIM64_INFINITY}) = 0  
futex(0x7f225d65e6fc, FUTEX_WAKE_PRIVATE, 2147483647) = 0  
getrandom("\x9f\x8c\xa0\xb6\x55\xec\x3d\x08", 8, GRND_NONBLOCK) = 8  
eventfd2(0, EFD_CLOEXEC|EFD_NONBLOCK)   = 3  
epoll_create1(EPOLL_CLOEXEC)            = 4  
timerfd_create(CLOCK_MONOTONIC, TFD_CLOEXEC) = 5  
epoll_ctl(4, EPOLL_CTL_ADD, 3, {events=EPOLLIN|EPOLLERR|EPOLLET, data={u32=37285688, u64=37285688}}) = 0  
write(3, "\1\0\0\0\0\0\0\0", 8)         = 8  
epoll_ctl(4, EPOLL_CTL_ADD, 5, {events=EPOLLIN|EPOLLERR, data={u32=37285700, u64=37285700}}) = 0  
pipe2([6, 7], 0)                        = 0  
fcntl(6, F_SETFL, O_RDONLY|O_NONBLOCK)  = 0  
fcntl(7, F_SETFL, O_RDONLY|O_NONBLOCK)  = 0  
fcntl(6, F_SETFD, FD_CLOEXEC)           = 0  
fcntl(7, F_SETFD, FD_CLOEXEC)           = 0  
epoll_ctl(4, EPOLL_CTL_ADD, 6, {events=EPOLLIN|EPOLLPRI|EPOLLERR|EPOLLHUP|EPOLLET, data={u32=37285888, u64=37285888}}) = 0  
rt_sigaction(SIGCHLD, {sa_handler=0x407a70, sa_mask=~[RTMIN RT_1], sa_flags=SA_RESTORER, sa_restorer=0x7f225d172eb0}, NULL, 8) = 0  
pipe2([8, 9], 0)                        = 0  
fcntl(9, F_SETFD, FD_CLOEXEC)           = 0  
clone(child_stack=NULL, flags=CLONE_CHILD_CLEARTID|CLONE_CHILD_SETTID|SIGCHLD, child_tidptr=0x7f225d6c6a10) = 1388960  
strace: Process 1388960 attached  
[pid 1388959] close(9)                  = 0  
[pid 1388960] set_robust_list(0x7f225d6c6a20, 24 <unfinished ...>  
[pid 1388959] read(8,  <unfinished ...>  
[pid 1388960] <... set_robust_list resumed>) = 0  
[pid 1388960] close(8)                  = 0  
[pid 1388959] <... read resumed>"", 8)  = 0  
[pid 1388959] close(8)                  = 0  
[pid 1388959] wait4(1388960, 0x7ffc5bd05754, WNOHANG, NULL) = 0  
[pid 1388959] kill(1388960, SIGKILL)    = 0  
[pid 1388959] wait4(1388960, 0x7ffc5bd05754, WNOHANG, NULL) = 0  
[pid 1388959] epoll_wait(4, [{events=EPOLLIN, data={u32=37285688, u64=37285688}}], 128, 0) = 1  
[pid 1388960] +++ killed by SIGKILL +++  
--- SIGCHLD {si_signo=SIGCHLD, si_code=CLD_KILLED, si_pid=1388960, si_uid=1337, si_status=SIGKILL, si_utime=0, si_stime=0} ---  
write(7, "\21\0\0\0", 4)                = 4  
rt_sigreturn({mask=[]})                 = 1  
wait4(1388960, [{WIFSIGNALED(s) && WTERMSIG(s) == SIGKILL}], WNOHANG, NULL) = 1388960  
write(2, "Child completed with exit code=", 31Child completed with exit code=) = 31  
write(2, "9", 19)                        = 1  
write(2, ", and error code: ", 18, and error code: )      = 18  
write(2, "Success", 7Success)                  = 7  
write(2, "\n", 1  
)                       = 1  
epoll_ctl(4, EPOLL_CTL_DEL, 6, 0x7ffc5bd05704) = 0  
close(6)                                = 0  
close(7)                                = 0  
rt_sigaction(SIGCHLD, {sa_handler=SIG_DFL, sa_mask=[], sa_flags=SA_RESTORER, sa_restorer=0x7f225d172eb0}, NULL, 8) = 0  
close(4)                                = 0  
close(5)                                = 0  
close(3)                                = 0  
exit_group(0)                           = ?  
+++ exited with 0 +++  
```  
</details>  
  
### boost 1.84 -> ECHILD  
  
<details>  
  
```console  
$ strace -f -e '!%file,%memory' result/bin/test   
arch_prctl(0x3001 /* ARCH_??? */, 0x7ffe02136880) = -1 EINVAL (Invalid argument)  
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\0\0\0\0\0\0\0\0"..., 832) = 832  
close(3)                                = 0  
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\0\0\0\0\0\0\0\0"..., 832) = 832  
close(3)                                = 0  
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\0\0\0\0\0\0\0\0"..., 832) = 832  
close(3)                                = 0  
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\0\0\0\0\0\0\0\0"..., 832) = 832  
close(3)                                = 0  
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\220\202\2\0\0\0\0\0"..., 832) = 832  
pread64(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784, 64) = 784  
pread64(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784, 64) = 784  
close(3)                                = 0  
arch_prctl(ARCH_SET_FS, 0x7ffad927f740) = 0  
set_tid_address(0x7ffad927fa10)         = 1387918  
set_robust_list(0x7ffad927fa20, 24)     = 0  
rseq(0x7ffad9280060, 0x20, 0, 0x53053053) = 0  
prlimit64(0, RLIMIT_STACK, NULL, {rlim_cur=8192*1024, rlim_max=RLIM64_INFINITY}) = 0  
futex(0x7ffad925e6fc, FUTEX_WAKE_PRIVATE, 2147483647) = 0  
getrandom("\xf2\xaf\x43\xf5\x80\x83\xac\x46", 8, GRND_NONBLOCK) = 8  
eventfd2(0, EFD_CLOEXEC|EFD_NONBLOCK)   = 3  
epoll_create1(EPOLL_CLOEXEC)            = 4  
timerfd_create(CLOCK_MONOTONIC, TFD_CLOEXEC) = 5  
epoll_ctl(4, EPOLL_CTL_ADD, 3, {events=EPOLLIN|EPOLLERR|EPOLLET, data={u32=25309008, u64=25309008}}) = 0  
write(3, "\1\0\0\0\0\0\0\0", 8)         = 8  
epoll_ctl(4, EPOLL_CTL_ADD, 5, {events=EPOLLIN|EPOLLERR, data={u32=25309020, u64=25309020}}) = 0  
pipe2([6, 7], 0)                        = 0  
fcntl(6, F_SETFL, O_RDONLY|O_NONBLOCK)  = 0  
fcntl(7, F_SETFL, O_RDONLY|O_NONBLOCK)  = 0  
fcntl(6, F_SETFD, FD_CLOEXEC)           = 0  
fcntl(7, F_SETFD, FD_CLOEXEC)           = 0  
epoll_ctl(4, EPOLL_CTL_ADD, 6, {events=EPOLLIN|EPOLLPRI|EPOLLERR|EPOLLHUP|EPOLLET, data={u32=25309200, u64=25309200}}) = 0  
rt_sigaction(SIGCHLD, {sa_handler=0x4073f0, sa_mask=~[RTMIN RT_1], sa_flags=SA_RESTORER, sa_restorer=0x7ffad8d72eb0}, NULL, 8) = 0  
pipe2([8, 9], 0)                        = 0  
fcntl(9, F_SETFD, FD_CLOEXEC)           = 0  
clone(child_stack=NULL, flags=CLONE_CHILD_CLEARTID|CLONE_CHILD_SETTID|SIGCHLD, child_tidptr=0x7ffad927fa10) = 1387919  
strace: Process 1387919 attached  
[pid 1387918] close(9)                  = 0  
[pid 1387919] set_robust_list(0x7ffad927fa20, 24 <unfinished ...>  
[pid 1387918] read(8,  <unfinished ...>  
[pid 1387919] <... set_robust_list resumed>) = 0  
[pid 1387919] close(8)                  = 0  
[pid 1387918] <... read resumed>"", 8)  = 0  
[pid 1387918] close(8)                  = 0  
[pid 1387918] wait4(1387919, 0x7ffe02136364, WNOHANG, NULL) = 0  
[pid 1387918] wait4(1387919, 0x7ffe021366f4, WNOHANG, NULL) = 0  
[pid 1387919] arch_prctl(0x3001 /* ARCH_??? */, 0x7ffda4ad19d0 <unfinished ...>  
[pid 1387918] kill(1387919, SIGKILL <unfinished ...>  
[pid 1387919] <... arch_prctl resumed>) = -1 EINVAL (Invalid argument)  
[pid 1387918] <... kill resumed>)       = 0  
[pid 1387918] wait4(1387919,  <unfinished ...>  
[pid 1387919] +++ killed by SIGKILL +++  
<... wait4 resumed>[{WIFSIGNALED(s) && WTERMSIG(s) == SIGKILL}], 0, NULL) = 1387919  
--- SIGCHLD {si_signo=SIGCHLD, si_code=CLD_KILLED, si_pid=1387919, si_uid=1337, si_status=SIGKILL, si_utime=0, si_stime=0} ---  
write(7, "\21\0\0\0", 4)                = 4  
rt_sigreturn({mask=[]})                 = 1387919  
epoll_wait(4, [{events=EPOLLIN, data={u32=25309008, u64=25309008}}, {events=EPOLLIN, data={u32=25309200, u64=25309200}}], 128, -1) = 2  
read(6, "\21\0\0\0", 4)                 = 4  
read(6, 0x7ffe0213651c, 4)              = -1 EAGAIN (Resource temporarily unavailable)  
epoll_wait(4, [], 128, 0)               = 0  
wait4(1387919, 0x7ffe02136250, WNOHANG, NULL) = -1 ECHILD (No child processes)  
write(2, "Child completed with exit code=", 31Child completed with exit code=) = 31  
write(2, "-1", 2-1)                       = 2  
write(2, ", and error code: ", 18, and error code: )      = 18  
write(2, "No child processes", 18No child processes)      = 18  
write(2, "\n", 1  
)                       = 1  
epoll_ctl(4, EPOLL_CTL_DEL, 6, 0x7ffe021366c4) = 0  
close(6)                                = 0  
close(7)                                = 0  
rt_sigaction(SIGCHLD, {sa_handler=SIG_DFL, sa_mask=[], sa_flags=SA_RESTORER, sa_restorer=0x7ffad8d72eb0}, NULL, 8) = 0  
close(4)                                = 0  
close(5)                                = 0  
close(3)                                = 0  
exit_group(0)                           = ?  
+++ exited with 0 +++  
```  
  
</details>
