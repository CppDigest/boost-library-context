# #238 - SIGSEV on udp socket [Closed]

> Username: ghost  
> Created at: 2019-05-16 08:15:00 UTC  
> Updated at: 2019-05-16 09:55:29 UTC  
> Closed at: 2019-05-16 09:55:29 UTC  
> Url: https://github.com/boostorg/asio/issues/238  

code here:  
```c++  
#include <boost/asio.hpp>  
#include <boost/asio/spawn.hpp>  
#include <iostream>  
  
namespace net = boost::asio;  
namespace sys = boost::system;  
  
void do_udp()  
{  
	net::io_context ioc;  
	net::steady_timer timer(ioc);  
	net::spawn(ioc, [&](net::yield_context yield)  
	{  
		try  
		{  
			while (true)  
			{  
				using namespace std::chrono_literals;  
				net::ip::udp::socket s(ioc);  
				net::ip::udp::endpoint pep{net::ip::make_address_v4("127.0.0.1"), 6666};  
				s.open(pep.protocol());  
				int data;  
				s.async_send_to(net::buffer(&data, 1), pep, yield);  
				timer.expires_after(1s);  
				timer.async_wait(yield);  
			}  
		}  
		catch (std::exception &e)  
		{  
			std::cout << e.what() << std::endl;  
		}  
	});  
	try  
	{  
		  
		ioc.run();  
	}  
	catch (std::exception &e)  
	{  
		std::cout << e.what() << std::endl;  
	}  
}  
  
int main()  
{  
	std::thread(&do_udp).detach();  
	net::io_context ioc;  
	net::ip::udp::socket s(ioc, {net::ip::make_address_v4("127.0.0.1"), 6666});  
	spawn(ioc, [&](net::yield_context yield)  
	{  
		try  
		{  
			std::array<uint8_t, 65536> buffer{};  
			net::ip::udp::endpoint pep;  
			while (true)  
			{  
				auto sz = s.async_receive_from(net::buffer(buffer), pep, yield);  
				std::stringstream ss;  
				ss << pep;  
				std::cout << ss.str() << "," << sz << std::endl;  
			}  
		}  
		catch (std::exception &e)  
		{  
			std::cout << e.what() << std::endl;  
		}  
		  
	});  
	try  
	{  
		ioc.run();  
	}  
	catch (std::exception &e)  
	{  
		std::cout << e.what() << std::endl;  
	}  
}  
```  
stacktrace here:  
```  
boost::asio::detail::io_object_impl<boost::asio::detail::win_iocp_socket_service<boost::asio::ip::udp>, boost::asio::executor>::get_service io_object_impl.hpp:153  
boost::asio::basic_datagram_socket<boost::asio::ip::udp, boost::asio::executor>::initiate_async_receive_from::operator()<boost::asio::detail::coro_handler<boost::asio::executor_binder<void (*)(), boost::asio::executor>, unsigned long long>, boost::asio::mutable_buffers_1> basic_datagram_socket.hpp:1105  
boost::asio::async_initiate<boost::asio::basic_yield_context<boost::asio::executor_binder<void (*)(), boost::asio::executor> >&, void (boost::system::error_code, unsigned long long), boost::asio::basic_datagram_socket<boost::asio::ip::udp, boost::asio::executor>::initiate_async_receive_from, boost::asio::basic_datagram_socket<boost::asio::ip::udp, boost::asio::executor>*, boost::asio::mutable_buffers_1 const&, boost::asio::ip::basic_endpoint<boost::asio::ip::udp>*, int>(boost::asio::basic_datagram_socket<boost::asio::ip::udp, boost::asio::executor>::initiate_async_receive_from&&, boost::asio::basic_yield_context<boost::asio::executor_binder<void (*)(), boost::asio::executor> >&, boost::asio::basic_datagram_socket<boost::asio::ip::udp, boost::asio::executor>*&&, boost::asio::mutable_buffers_1 const&, boost::asio::ip::basic_endpoint<boost::asio::ip::udp>*&&, int&&) async_result.hpp:273  
boost::asio::basic_datagram_socket<boost::asio::ip::udp, boost::asio::executor>::async_receive_from<boost::asio::mutable_buffers_1, boost::asio::basic_yield_context<boost::asio::executor_binder<void (*)(), boost::asio::executor> >&> basic_datagram_socket.hpp:990  
<lambda(boost::asio::yield_context)>::operator()(boost::asio::yield_context) const main.cpp:58  
boost::asio::detail::coro_entry_point<boost::asio::executor_binder<void (*)(), boost::asio::strand<boost::asio::io_context::executor_type> >, main()::<lambda(boost::asio::yield_context)> >::operator()(boost::asio::basic_yield_context<boost::asio::executor_binder<void (*)(), boost::asio::strand<boost::asio::io_context::executor_type> > >::caller_type &) spawn.hpp:337  
boost::coroutines::detail::push_coroutine_object<boost::coroutines::pull_coroutine<void>, void, boost::asio::detail::coro_entry_point<boost::asio::executor_binder<void (*)(), boost::asio::strand<boost::asio::io_context::executor_type> >, main()::<lambda(boost::asio::yield_context)> >&, boost::coroutines::basic_standard_stack_allocator<boost::coroutines::stack_traits> >::run(void) push_coroutine_object.hpp:302  
boost::coroutines::detail::trampoline_push_void<boost::coroutines::detail::push_coroutine_object<boost::coroutines::pull_coroutine<void>, void, boost::asio::detail::coro_entry_point<boost::asio::executor_binder<void (*)(), boost::asio::strand<boost::asio::io_context::executor_type> >, main()::<lambda(boost::asio::yield_context)> >&, boost::coroutines::basic_standard_stack_allocator<boost::coroutines::stack_traits> > >(boost::context::detail::transfer_t) trampoline_push.hpp:70  
trampoline 0x0000000000405766  
<unknown> 0xbaadf00dbaadf00d  
```  
  
compiler version: msys2 mingw 8.3.0   
boost version: 1.70.0  
with build flags: default falgs  
os: windows 10  
gcc -v  
```  
$ gcc -v  
Using built-in specs.  
COLLECT_GCC=E:\msys2\mingw64\bin\gcc.exe  
COLLECT_LTO_WRAPPER=E:/msys2/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/8.3.0/lto-wrapper.exe  
Target: x86_64-w64-mingw32  
Configured with: ../gcc-8.3.0/configure --prefix=/mingw64 --with-local-prefix=/mingw64/local --build=x86_64-w64-mingw32 --host=x86_64-w64-mingw32 --target=x86_64-w64-mingw32 --with-native-system-header-dir=/mingw64/x86_64-w64-mingw32/include --libexecdir=/mingw64/lib --enable-bootstrap --with-arch=x86-64 --with-tune=generic --enable-languages=ada,c,lto,c++,objc,obj-c++,fortran --enable-shared --enable-static --enable-libatomic --enable-threads=posix --enable-graphite --enable-fully-dynamic-string --enable-libstdcxx-filesystem-ts=yes --enable-libstdcxx-time=yes --disable-libstdcxx-pch --disable-libstdcxx-debug --disable-isl-version-check --enable-lto --enable-libgomp --disable-multilib --enable-checking=release --disable-rpath --disable-win32-registry --disable-nls --disable-werror --disable-symvers --with-libiconv --with-system-zlib --with-gmp=/mingw64 --with-mpfr=/mingw64 --with-mpc=/mingw64 --with-isl=/mingw64 --with-pkgversion='Rev2, Built by MSYS2 project' --with-bugurl=https://sourceforge.net/projects/msys2 --with-gnu-as --with-gnu-ld  
Thread model: posix  
gcc version 8.3.0 (Rev2, Built by MSYS2 project)  
  
```  
```
