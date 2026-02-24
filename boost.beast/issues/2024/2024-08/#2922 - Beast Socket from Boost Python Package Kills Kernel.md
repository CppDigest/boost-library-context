# #2922 - Beast Socket from Boost Python Package Kills Kernel [Closed]

> Username: MasterBe  
> Created at: 2024-08-30 18:09:00 UTC  
> Updated at: 2024-10-16 07:18:42 UTC  
> Closed at: 2024-10-16 07:18:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2922  

For some reason when exposing the below to python using Boost Python, the unique pointer constructor (the obj constructor) fails. This used to work for me before, not sure why it stopped.  
  
```  
#pragma once  
#include <memory>  
#include <boost/asio/ssl.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/beast.hpp>  
  
//Socket  
class Contextable  
{  
public:  
	Contextable() : m_ssl(boost::asio::ssl::context::sslv23_client) {}  
  
protected:  
	boost::asio::io_context    m_ctx;  
	boost::asio::ssl::context  m_ssl;  
};  
  
class SocketExample : protected Contextable  
{  
	using SocketImp = boost::beast::websocket::stream<boost::asio::ssl::stream<boost::asio::ip::tcp::socket>>;  
public:  
	SocketExample()  
	{  
	}  
  
	void constructSocket()  
	{  
		m_ws = std::make_unique<SocketImp>(m_ctx, m_ssl); //kills jupyter kernel  
		std::cout << "DONE. OPEN FLAG: " << m_ws->is_open() << std::endl;  
	}  
  
private:  
	std::unique_ptr<SocketImp> m_ws;  
};  
  
//ExampleModule  
#include <boost/python.hpp>  
  
BOOST_PYTHON_MODULE(ExampleModule)  
{  
    boost::python::class_<SocketExample, std::shared_ptr<SocketExample>, boost::noncopyable>("SocketExample")  
        .def(boost::python::init<>())  
        .def("constructSocket", &SocketExample::constructSocket)  
        ;  
}  
```  
  
```  
##Jupyter Notebook  
import ExampleModule as m  
socket = m.SocketExample()  
socket.constructSocket() ##kills kernel  
```  
  
Any idea what I am doing wrong? When running this using c++ as follows does not fail.  
```  
SocketExample sk;  
sk.constructSocket(); //works fine when run inside main.cpp  
```

---

## Comment 1

> Username: ashtum  
> Created at: 2024-08-30 18:16:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2922#issuecomment-2322097397  

What version of Boost are you using? Are there any errors or messages that might help?

---

## Comment 2

> Username: MasterBe  
> Created at: 2024-08-30 19:15:33 UTC  
> Updated at: 2024-08-30 19:33:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2922#issuecomment-2322186072  

Hi I am using boost version 1_84 (```#define BOOST_VERSION 108400```). I am running on release mode no errors/messages, I tried to try-catch and cout to screen but the error is not catchable just kills kernel. I tried constructing the socket step by step and I can without error execute the following code from python:  
```  
	std::cout << "START" << std::endl;  
	using NextLayer = boost::asio::ssl::stream<boost::asio::ip::tcp::socket>;  
	using ws_stream = boost::beast::websocket::stream<NextLayer>;  
	boost::asio::io_context m_ctx{};  
	boost::asio::ssl::context m_ssl(boost::asio::ssl::context::sslv23_client);  
	boost::empty_value<NextLayer>(boost::empty_init_t{}, m_ctx, m_ssl);  
	//auto m_stream = std::make_unique<ws_stream::impl_type>(m_ctx, m_ssl);  
	//std::cout << "OPEN: 139 < " << m_stream->rd_buf.max_size() << std::endl;  
	std::cout << "END" << std::endl;  
```  
  
I made ```impl_type``` public for the sake of debugging.  Still this: ```auto m_stream = std::make_unique<ws_stream::impl_type>(m_ctx, m_ssl);``` fails  
  
The boost code is:  
```  
    template<class... Args>  
    impl_type(Args&&... args)  
        : boost::empty_value<NextLayer>(  
            boost::empty_init_t{},  
            std::forward<Args>(args)...)  
        , detail::service::impl_type(  
            this->get_context(  
                this->boost::empty_value<NextLayer>::get().get_executor()))  
        , timer(this->boost::empty_value<NextLayer>::get().get_executor())  
    {  
        timeout_opt.handshake_timeout = none();  
        timeout_opt.idle_timeout = none();  
        timeout_opt.keep_alive_pings = false;  
    }  
```  
  
So looks like it's the second part failing. (something to do with context maybe)

---

## Comment 3

> Username: MasterBe  
> Created at: 2024-08-30 20:09:39 UTC  
> Updated at: 2024-08-30 20:31:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2922#issuecomment-2322263330  

I can track it down to here:  
```  
	std::cout << "START" << std::endl;  
	struct impex : public boost::beast::websocket::detail::service::impl_type  
	{  
		impex(boost::asio::execution_context& ctx) : boost::beast::websocket::detail::service::impl_type(ctx) {};  
		virtual  
			void  
			shutdown() {}  
	};  
  
	boost::asio::execution_context ctx;  
	impex v(ctx); //kills kernel  
	std::cout << "END" << std::endl;  
```  
  
Looking at: ```include\boost\asio\execution_context.hpp```. Not sure who is initializing: ```boost::asio::detail::service_registry* service_registry_;```?

---

## Comment 4

> Username: sehe  
> Created at: 2024-08-30 20:49:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2922#issuecomment-2322318906  

For info, I answered the same question on StackOverflow https://stackoverflow.com/a/78933665/85371 where I was able to demonstrate it working from a Jupyter Notebook in principle. I also imagined a technical reason why things would stop working, although @MasterBe 's problem ostensibly has a different exact root cause.  
  
Given the extra info in this ticket, I'd still look at compiler flags and shared libraries (ldconf/SxS/manifests depending on platforms)
