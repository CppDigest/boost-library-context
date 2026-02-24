# #832 - The connection cannot be completed in Linux boost::asio::spawn [Open]

> Username: codinghahaha  
> Created at: 2023-11-23 07:25:21 UTC  
> Updated at: 2023-11-23 07:26:40 UTC  
> Url: https://github.com/boostorg/boost/issues/832  

If the server starts after the connection cannot be reached.why?  
boost_1_73_0、Linux operating system  
#include <iostream>  
#include "boost/asio.hpp"  
#include "boost/asio/spawn.hpp"  
  
int main()  
{  
	boost::asio::io_context ctx;  
	auto ep = boost::asio::ip::tcp::endpoint(boost::asio::ip::make_address("127.0.0.1"), 1560);  
	bool isConnect = false;  
	bool isRun = false;  
	boost::asio::ip::tcp::socket socket(ctx);  
	while (true)  
	{  
		if (!isConnect && !isRun)  
		{  
			boost::asio::spawn([&](boost::asio::yield_context y){  
				isRun = true;  
				boost::system::error_code ec;  
				socket.async_connect(ep, y[ec]);  
				if (ec)  
				{  
					isRun = false;  
					socket.close();  
					printf("close\n");  
				}  
				else  
				{  
					isConnect = true;  
					printf("connect\n");  
				}  
			});  
		}  
		ctx.poll_one();  
		Sleep(1000);  
	}  
	return 0;  
}
