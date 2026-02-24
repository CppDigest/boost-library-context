# #353 - pipe_out leaks pipe source fd when redirecting both stdout and stderr to same pipe [Closed]

> Username: dkl  
> Created at: 2024-02-05 10:57:25 UTC  
> Updated at: 2024-06-04 00:54:22 UTC  
> Closed at: 2024-06-04 00:54:22 UTC  
> Url: https://github.com/boostorg/process/issues/353  

Hello,  
  
Since commit caa7b2fcc8e9518f4b6ab0d5d7b3adfac1068415, `pipe_out<1,2>::on_exec_setup()` is missing a `::close(source);` call as in `pipe_out<1,-1>::on_exec_setup()` and `pipe_out<2,-1>::on_exec_setup()`. This leads to the pipe source fd being leaked in the child process, keeping the read end of the pipe open unnecessarily (parent process cannot close it).  
  
An example where the difference is visible:  
```c++  
#include <boost/asio/io_context.hpp>  
#include <boost/process/child.hpp>  
#include <boost/process/io.hpp>  
#include <boost/process/args.hpp>  
#include <boost/process/async_pipe.hpp>  
  
struct runner {  
	boost::asio::io_context ioctx;  
	boost::process::async_pipe stdout_pipe;  
	boost::process::child child_process;  
  
	runner()  
		: stdout_pipe(ioctx)  
		, child_process{  
			boost::process::argv({"sh", "-c", "sleep 1; echo test"}),  
			(boost::process::std_out & boost::process::std_err) > stdout_pipe,  
		}  
	{  
		stdout_pipe.close();  
	}  
  
	void run()  
	{  
		ioctx.run();  
		child_process.wait();  
		printf("child exit code %i\n", child_process.exit_code());  
	}  
};  
  
int main()  
{  
	runner r;  
	r.run();  
}  
```  
  
Without the `close(source)`, it prints:  
```  
child exit code 0  
```  
(unexpected: child process didn't fail even though the parent closes the pipe)  
  
With the `close(source)`, it prints:  
```  
child exit code 13  
```  
(expected: child process failed due to SIGPIPE when writing to stdout, as expected because the parent closes the pipe)
