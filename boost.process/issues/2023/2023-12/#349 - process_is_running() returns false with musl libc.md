# #349 - [v2][Linux] process_is_running() returns false with musl libc [Open]

> Username: ceggers-arri  
> Created at: 2023-12-20 11:03:04 UTC  
> Updated at: 2023-12-20 11:03:04 UTC  
> Url: https://github.com/boostorg/process/issues/349  

**Observed with boost-1.81.0**  
  
On Linux, the default _exit code_ (`detail::still_active`) is defined as `0x7f`. But on systems with musl libc (e.g. Alpine Linux and some OpenEmbedded/Yocto variants), `WIFSIGNALED(0x7F)` results in a non zero value. This causes a wrong return value in `process_is_runing()`:  
  
```  
inline bool process_is_running(int code)  
{  
    return !WIFEXITED(code) && !WIFSIGNALED(code);  
}  
```  
  
When `process_is_running()` is called with the initial value (`detail::still_active`) of `basic_process::exit_status_` (via `async_wait_op_::res` in `async_wait_op_::operator()()`) , this leads to premature completion (and termination) of a newly created process.  
  
**Test program:**  
```  
#include <features.h>  
  
#ifndef __GLIBC__  
/* close_range() hasn't been accepted for musl-libc yet:  
 * https://www.openwall.com/lists/musl/2023/09/01/4  
 */  
int close_range(unsigned int, unsigned int, int);  
  
#ifndef _GNU_SOURCE  
#define _GNU_SOURCE  
#endif  
#include <unistd.h>  
#include <syscall.h>  
  
int close_range(unsigned int first, unsigned int last, int flags)  
{  
	return syscall(__NR_close_range, first, last, flags);  
}  
#endif  
  
#include <iostream>  
  
#include <boost/asio/io_context.hpp>  
#include <boost/process/v2/execute.hpp>  
#include <boost/process/v2/process.hpp>  
#include <boost/system/error_code.hpp>  
  
int main(int, char **)  
{  
	using namespace boost::process::v2;  
  
	boost::asio::io_context ctx;  
	async_execute(process(ctx, "/bin/echo", {"1", "2", "3", "4"}),  
		[](boost::system::error_code const &ec, int exitCode)  
		{  
			if (ec) {  
				std::cerr << "Executing process failed: " << ec << '\n';  
				return;  
			}  
  
			std::cout << "Process existed with status " << exitCode << '\n';  
		});  
  
	ctx.run();  
}  
```  
  
Output _Glibc_ based systems (correct):  
```  
1 2 3 4  
Process existed with status 0  
```  
  
Possible output in _musl libc_ based systems (not correct, may vary due to timing):  
```  
Process existed with status 127  
```  
  
Note that `127` here is the initial value of `basic_process::exit_status_` rather than the real exit code of `/bin/echo`. In my tests, the `echo` process is usually killed with `SIGKILL` before producing any output:  
  
```  
process::v2::detail::execute_op::~execute_op()  
\-std::unique_ptr<v2::basic_process>::~unique_ptr()  [execute_op::proc member]  
  \-...  
    \-basic_process::~basic_process()  
      \-detail::basic_process_handle_fd<...>::terminate_if_running()  
        \-::kill(pid_, SIGKILL);  
```
