# #349 Fix race condition where child leaks signal to parent [Open]

> Username: tbluemel  
> Created at: 2020-05-08 23:29:08 UTC  
> Updated at: 2020-05-08 23:29:08 UTC  
> Url: https://github.com/boostorg/asio/pull/349  

If a child process received a signal right after fork()  
but before notify_fork(fork_child) was called, the signal  
is leaked to the parent process, causing it to believe it  
received that signal instead of the child.  
  
This is some sample code to reproduce the issue:  
```  
// Compile & run: g++ -pthread -o signal_fork_leak signal_fork_leak.cpp -lboost_system && ./signal_fork_leak  
//  
// Output with fix (expected):  
//  child created, sending SIGUSR1 to child  
//  child running  
//  child received signal 10  
//  sending SIGUSR2 to child  
//  child received signal 12  
//  child exiting  
//  child exited with code 0  
//  
// Output without fix:  
//  child created, sending SIGUSR1 to child  
//  child running  
//  sending SIGUSR2 to child  
//  parent received signal 10  
//  child received signal 12  
//  child exiting  
//  child exited with code 0  
  
#include <sys/types.h>  
#include <sys/wait.h>  
#include <signal.h>  
#include <boost/asio.hpp>  
#include <iostream>  
  
pid_t g_parent = ::getpid();  
  
static void signal_handler(boost::asio::signal_set& signals, const boost::system::error_code& err, int signal_number)  
{  
	if (!err)  
	{  
		if (signal_number == SIGCHLD)  
		{  
			int status = 0;  
			pid_t pid = ::waitpid(-1, &status, WNOHANG);  
			if (pid > 0)  
			{  
				if (WIFEXITED(status))  
				{  
					std::cout << "child exited with code " << WEXITSTATUS(status) << std::endl;  
					return;  
				}  
				else if (WIFSIGNALED(status))  
				{  
					std::cout << "child exited due to unhandled signal " << WTERMSIG(status) << std::endl;  
					return;  
				}  
			}  
		}  
		else  
		{  
			if (g_parent == ::getpid())  
				std::cout << "parent received signal " << signal_number << std::endl;  
			else  
				std::cout << "child received signal " << signal_number << std::endl;  
		}  
		signals.async_wait(std::bind(signal_handler, std::ref(signals), std::placeholders::_1, std::placeholders::_2));  
	}  
}  
  
int main()  
{  
	boost::asio::io_context io;  
	boost::asio::signal_set signals(io, SIGUSR1, SIGUSR2, SIGCHLD);  
	boost::asio::steady_timer tmr(io);  
	signals.async_wait(std::bind(signal_handler, std::ref(signals), std::placeholders::_1, std::placeholders::_2));  
	boost::asio::post(io,  
		[&]()  
		{  
			io.notify_fork(boost::asio::execution_context::fork_prepare);  
			pid_t p = ::fork();  
			if (p == -1)  
			{  
				int err = errno;  
				std::cerr << "fork() failed with error " << err << std::endl;  
			}  
			else if (p == 0)  
			{  
				std::cout << "child running" << std::endl;  
				  
				// sleep for a second to simulate delay  
				std::this_thread::sleep_for(std::chrono::seconds(1));  
				  
				io.notify_fork(boost::asio::execution_context::fork_child);  
				tmr.expires_after(std::chrono::seconds(2));  
				tmr.async_wait(  
					[&](const boost::system::error_code& err)  
					{  
						if (!err)  
						{  
							std::cout << "child exiting" << std::endl;  
							io.stop();  
						}  
					});  
			}  
			else  
			{  
				io.notify_fork(boost::asio::execution_context::fork_parent);  
				std::cout << "child created, sending SIGUSR1 to child" << std::endl;  
				if (::kill(p, SIGUSR1) == -1)  
				{  
					int err = errno;  
					std::cerr << "kill() failed with error " << err << std::endl;  
				}  
				std::this_thread::sleep_for(std::chrono::seconds(2));  
				std::cout << "sending SIGUSR2 to child" << std::endl;  
				if (::kill(p, SIGUSR2) == -1)  
				{  
					int err = errno;  
					std::cerr << "kill() failed with error " << err << std::endl;  
				}  
			}  
		});  
	io.run();  
	return 0;  
}  
```

---
