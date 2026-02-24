# #231 - boost::process::async_system in composed asio operations [Open]

> Username: ceggers-arri  
> Created at: 2021-11-19 15:48:54 UTC  
> Updated at: 2022-06-24 05:42:42 UTC  
> Url: https://github.com/boostorg/process/issues/231  

Boost version: 1.72.0  
  
I would like to use boost::process::async_system() within a composed operation (example taken from [composed_6.cpp](https://www.boost.org/doc/libs/1_72_0/doc/html/boost_asio/example/cpp14/operations/composed_6.cpp)).   
  
The helper boost::asio::async_compose() creates a temporary object (boost::asio::detail::composed_op<>) which contains all necessary variables/states during the whole composed operation. Unfortunately my program (below) doesn't compile. The (lengthy) compiler message looks like boost::process::async_system() tries to make another copy of the completion object (self). But boost::asio::detail::composed_op<> is not copyable.  
  
Is it possible to avoid making an extra copy of the completion object in boost::process::async_system() (like the other boost::asio::async_xxx functions behave)?  
  
regards  
Christian  
  
```C++  
#include <cerrno>  
#include <memory>  
#include <iostream>  
#include <string>  
#include <utility>  
  
#include <boost/asio/buffer.hpp>  
#include <boost/asio/compose.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/local/stream_protocol.hpp>  
#include <boost/filesystem/operations.hpp>  
#include <boost/process/args.hpp>  
#include <boost/process/async_system.hpp>  
#include <boost/process/env.hpp>  
#include <boost/process/environment.hpp>  
#include <boost/process/exe.hpp>  
#include <boost/process/extend.hpp>  
#include <boost/process/handles.hpp>  
#include <boost/process/io.hpp>  
#include <boost/process/posix.hpp>  
#include <boost/system/error_code.hpp>  
  
  
static constexpr char const *gpsdExecutable = "/usr/sbin/gpsd";           // always absolute, also on host  
static constexpr char const *gpsdControlSocket = "/run/gpsd.sock";  // always absolute, also on host  
  
static void prepareGpsdSockets(  
		boost::asio::local::stream_protocol::acceptor &controlSocket,  
		boost::asio::ip::tcp::acceptor &exportSocket,  
		boost::asio::ip::tcp::resolver::results_type const &endpoints,  
		boost::system::error_code &ec)  
{  
	/* control socket */  
	boost::filesystem::remove(gpsdControlSocket, ec);  // ignore errors  
	boost::asio::local::stream_protocol::endpoint controlSocketEp(gpsdControlSocket);  
	controlSocket.open(controlSocketEp.protocol(), ec);  
	if (ec)  
		return;  
	controlSocket.bind(controlSocketEp, ec);  
	if (ec)  
		return;  
	controlSocket.listen(boost::asio::socket_base::max_listen_connections, ec);  
	if (ec)  
		return;  
  
	/* export socket TCPv4 */  
	boost::asio::ip::tcp::endpoint const &exportSocketEp = *endpoints.begin();  
	exportSocket.open(exportSocketEp.protocol(), ec);  
	if (ec)  
		return;  
	exportSocket.set_option(boost::asio::socket_base::reuse_address(true), ec);  
	if (ec)  
		return;  
	exportSocket.bind(exportSocketEp, ec);  
	if (ec)  
		return;  
	exportSocket.listen(boost::asio::socket_base::max_listen_connections, ec);  
	if (ec)  
		return;  
}  
  
template<typename CompletionToken>  
inline auto async_startGpsd(boost::asio::io_context &ioContext, CompletionToken &&token)  
{  
	enum class State { starting, resolving, forking };  
  
	return boost::asio::async_compose<CompletionToken, void(boost::system::error_code)>(  
		[  
			state         = State::starting,  
			&ioContext,  
			/* Note: std::make_shared() doesn't help here. */  
			controlSocket = std::make_unique<boost::asio::local::stream_protocol::acceptor>(ioContext),  
			resolver      = std::make_unique<boost::asio::ip::tcp::resolver>(ioContext),  
			exportSocket  = std::make_unique<boost::asio::ip::tcp::acceptor>(ioContext)  
		]  
		(  
			auto &self,   // reference to intermediate completion handle (provided by async_compose)  
			boost::system::error_code ec = {},  
			boost::asio::ip::tcp::resolver::results_type endpoints = {},  
			int exitCode = {}  
		) mutable  
		{  
			if (ec)  
			{  
				self.complete(ec);  
				return;  
			}  
  
			switch (state)  
			{  
				case State::starting:  
				{  
					state = State::resolving;  
  
					/* lookup gpsd port number from /etc/services */  
					resolver->async_resolve(boost::asio::ip::tcp::v4(), "", "gpsd", boost::asio::ip::resolver_base::flags::passive, std::move(self));  
					return;  // Composed operation not yet complete.  
				}  
  
				case State::resolving:  
				{  
					state = State::forking;  
					prepareGpsdSockets(*controlSocket, *exportSocket, endpoints, ec);  
					if (ec)  
						break;  
  
					auto controlHandle = controlSocket->native_handle();  
					auto exportHandle = exportSocket->native_handle();  
					boost::process::async_system(  
						ioContext,  
						[self=std::move(self)]  
						(boost::system::error_code ec, int exitCode)  
						mutable  
						{  
							self(ec, boost::asio::ip::tcp::resolver::results_type(), exitCode);  
						},  
						boost::process::exe = gpsdExecutable,  
						boost::process::args = {  
							"--listenany",  
						},  
						/* the order of bind() and close() is important */  
						boost::process::std_in  < stdin,  // don't close std handles  
						boost::process::std_out > stdout,  
						boost::process::std_err > stderr,  
						boost::process::posix::fd.bind(3, controlHandle),  
						boost::process::posix::fd.bind(4, exportHandle),  
						boost::process::posix::fd.close(controlHandle),  
						boost::process::posix::fd.close(exportHandle),  
						boost::process::limit_handles,  
						boost::process::extend::on_exec_setup = [](auto &executor)  
						{  
							auto env = boost::this_process::environment();  
							env["LISTEN_PID"] = std::to_string(boost::this_process::native_handle());  
							env["LISTEN_FDS"] = "2";  // number of sockets  
  
							/* use latest ::environ for ::execve() */  
							executor.env = env.native_handle();  
						}  
					);  
					return;  // Composed operation not yet complete.  
				}  
  
				case State::forking:  
					if (exitCode)  // error code from gpsd  
					{  
						ec.assign(ESRCH /* No such process */, boost::system::system_category());  
					}  
					break; // Composed operation complete, continue below.  
			}  
  
			// Call the user-supplied handler with the result of the operation.  
			self.complete(ec);  
		},  
		token, ioContext);  
}  
  
int main(void)  
{  
	boost::asio::io_context ioContext;  
  
	async_startGpsd(ioContext,  
		[](boost::system::error_code const &ec)  
		{  
			std::cout << "async_startGpsd() finished with ec = \n" << ec;  
		});  
}  
  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-06-11 06:42:21 UTC  
> Url: https://github.com/boostorg/process/issues/231#issuecomment-1152868338  

Yes, that would've been the right approach. Can you test if `v2::async_execute` would work for you?

---

## Comment 2

> Username: ceggers-arri  
> Created at: 2022-06-23 08:01:05 UTC  
> Url: https://github.com/boostorg/process/issues/231#issuecomment-1164084358  

I didn't recognize that there is a `v2` of `boost::process` before. Yesterday I spent some time for porting my example.   
  
Some questions about the docs:  
  
- [Simplified Interface](https://www.boost.org/doc/libs/develop/doc/html/boost_process/v2.html#boost_process.v2.introduction.simplified)  
  - "_Every initializer adresses one logical compoent (e.g. stdio) instead of multiple ones accumulating._"  
    - This seems not to be true for my example below. I use multiple `bind_fd` arguments  
- [Quickst**ra**t](https://www.boost.org/doc/libs/develop/doc/html/boost_process/v2.html#boost_process.v2.quickstart) --> Quickst**ar**t  
  - "_an asio execution_context / executor_" is required. But this is missing in some examples.  
- [Execute functions](https://www.boost.org/doc/libs/develop/doc/html/boost_process/v2.html#boost_process.v2.quickstart.execute)  
  - What are "managed" executions?  
 - [Environment](https://www.boost.org/doc/libs/develop/doc/html/boost_process/v2.html#boost_process.v2.env)  
   - The examples seem to fully replace the environment. How to append single variables to the existing env (which is inherited by the parent)?  
 - Extensions: I found no documentation about custom extensions (like setting the env as in the example below).  
  
I started with the **blocking** example:  
  
```  
#include <boost/process/v2/environment.hpp>  
#include <boost/process/v2/execute.hpp>  
#include <boost/process/v2/pid.hpp>  
#include <boost/process/v2/process.hpp>  
#include <boost/process/v2/posix/bind_fd.hpp>  
#include <boost/process/v2/stdio.hpp>  
  
void GpsdManager::startGpsd(boost::system::error_code &ec)  
{  
...  
	/// Initializer for adding systemd environment variables of a subprocess to be launched.  
	struct set_systemd_env  
	{  
		std::error_code on_exec_setup(boost::process::v2::posix::default_launcher &launcher,  
							   const boost::filesystem::path &, const char * const *)  
		{  
			/* as ${LISTEN_PID} must match the forked process, we have to  
			 * alter the environment from the child process.  
			 */  
			/* launcher.pid is set to 0 (as returned by ::fork()) */  
			auto pid = std::to_string(boost::process::v2::current_pid());  
			boost::process::v2::environment::set("LISTEN_PID", pid);  
			boost::process::v2::environment::set("LISTEN_FDS", "2");  
  
			/* use latest ::environ for ::execve() */  
			launcher.env = boost::process::v2::environment::detail::load_native_handle();  
  
			return std::error_code();  
		}  
	};  
  
	int ret = boost::process::v2::execute(  
		boost::process::v2::process(  
			m_ioContext,  
			gpsdExecutable,  
			{"--listenany"},  
			boost::process::v2::process_stdio{nullptr, nullptr, nullptr},  
			boost::process::v2::posix::bind_fd(3, controlSocket),  
			boost::process::v2::posix::bind_fd(4, exportSocket),  
			set_systemd_env{}  
		)  
	);  
}  
```  
  
**[Fd safe by default](https://www.boost.org/doc/libs/develop/doc/html/boost_process/v2.html#boost_process.v2.introduction.limit_fd)**  
I tested the "fd safety" feature and noticed some issues:  
- [signal-safety(7)](https://man7.org/linux/man-pages/man7/signal-safety.7.html)  
  - The (fork(2) manpage)[https://man7.org/linux/man-pages/man2/fork.2.html] states the following:  
  "_After a fork() in a multithreaded program, the child can  
          safely call only async-signal-safe functions (see  
          signal-safety(7)) until such time as it calls execve(2)._"  
  - You use `std::vector` for `fd_whitelist`. I assume that dynamic memory allocation is not _async-signal-safe_.  
    - You might preallocate the required vector size in `setup()`.  
- `fd_whitelist` contains some file descriptors twice  
  - init: {0, 1, 2}  
  - after `process_stdio::on_exec_setup()`: {0, 1, 2, 0, 1, 2}  
  - after bind_fd::on_exec_setup(): {0, 1, 2, 0, 1, 2, 3}  
  - after bind_fd::on_exec_setup(): {0, 1, 2, 0, 1, 2, 3, 4}  
  - fd_whitelist.push_back(pg.p[1]): {0, 1, 2, 0, 1, 2, 3, 4, 13}  
  - std::sort(fd_whitelist.begin(), fd_whitelist.end()): {0, 0, 1, 1, 2, 2, 3, 4, 13}  
  - `v2::posix::detail::close_all()` has a copy/paste issue:  
```  
            const auto mine = whitelist[idx];  
            const auto next = whitelist[idx];  
```  
  - `close_range()` is missing in my musl-libc (but my Linux supports it).

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2022-06-24 05:42:41 UTC  
> Url: https://github.com/boostorg/process/issues/231#issuecomment-1165218659  

Well v2 is fresh of the printing press and still experimental - I'll work on improving it over the next release cycle. Thanks for the remarks on the docs, I shall improve them.  
  
`bind_fd` is the one exception I think, but even there you can bind multiple ones in once go.  
  
The fd_whitelist is allowed to contain them twice on purpose, but reserving might be a good idea. I'll look into the signal safety.
