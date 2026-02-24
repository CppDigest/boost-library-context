# #448 - Beast Socket from Boost Python Package Kills Kernel [Open]

> Username: MasterBe  
> Created at: 2024-08-30 15:18:57 UTC  
> Updated at: 2024-08-30 15:18:57 UTC  
> Url: https://github.com/boostorg/python/issues/448  

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
