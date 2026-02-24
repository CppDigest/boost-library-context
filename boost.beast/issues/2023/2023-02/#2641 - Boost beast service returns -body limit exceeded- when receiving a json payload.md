# #2641 - Boost beast service returns "body limit exceeded" when receiving a json payload [Closed]

> Username: thm-development  
> Created at: 2023-02-17 12:57:03 UTC  
> Updated at: 2023-08-19 07:33:58 UTC  
> Closed at: 2023-08-19 07:33:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2641  

Hello,   
I'm working on a project which implement a boost beast service. This part of code was written by a person who left the company and I do not master boot. Until now it worked perfectly but the size of the payload has increased and it no longer works. The payload is about 2.4MB.  
The service is implemented using 3 classes ServerService, Listener and Session.  
  
**ServerService:**  
```  
void ServerService::startServer(const std::string& address, const unsigned short& port,  
                                 const std::string& baseRessourceName, const unsigned short& threadNumber)  
{  
    try  
    {  
        const auto srvAddress = boost::asio::ip::make_address(address);  
  
        // The io_context is required for all I/O  
        auto const nbThreads = std::max<int>(1, threadNumber);  
        boost::asio::io_context ioContext(nbThreads);  
  
        // Create listener and launch a listening port  
        std::shared_ptr<Listener> listener = std::make_shared<Listener>(ioContext, tcp::endpoint{ srvAddress, port }, baseRessourceName);  
        listener->run();  
  
        // Run the I/O service on the requested number of threads  
        std::vector<std::thread> threads;  
        threads.reserve(nbThreads - 1);  
        for (auto i = nbThreads - 1; i > 0; --i)  
        {  
            threads.emplace_back([&ioContext] { ioContext.run(); });  
        }  
        ioContext.run();  
    }  
    catch (std::exception const& e)  
    {  
        LBC_ERROR("{}", e.what());  
    }  
}  
```  
  
**Listener:**  
```  
// Used namespace  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
  
namespace Http  
{  
    class Listener : public std::enable_shared_from_this<Listener>  
    {  
    private:  
        tcp::acceptor m_acceptor;  
        tcp::socket m_socket;  
        std::string const& m_baseResourceName;  
  
        // Report a failure  
        void logError(boost::system::error_code errorCode, char const* what)  
        {  
            LBC_ERROR("{}: {}", what, errorCode.message());  
        }  
  
    public:  
        Listener(boost::asio::io_context& ioContext, tcp::endpoint endpoint, std::string const& docRoot)  
            : m_acceptor(ioContext)  
            , m_socket(ioContext)  
            , m_baseResourceName(docRoot)  
        {  
            boost::system::error_code errorCode;  
  
            // Open the acceptor  
            m_acceptor.open(endpoint.protocol(), errorCode);  
            if (errorCode)  
            {  
                logError(errorCode, "open");  
                return;  
            }  
  
            // Allow address reuse  
            m_acceptor.set_option(boost::asio::socket_base::reuse_address(true));  
            if (errorCode)  
            {  
                logError(errorCode, "set_option");  
                return;  
            }  
  
            // Bind to the server address  
            m_acceptor.bind(endpoint, errorCode);  
            if (errorCode)  
            {  
                logError(errorCode, "bind");  
                return;  
            }  
  
            // Start listening for connections  
            m_acceptor.listen(boost::asio::socket_base::max_listen_connections, errorCode);  
            if (errorCode)  
            {  
                logError(errorCode, "listen");  
                return;  
            }  
        }  
  
        // Start accepting incoming connections  
        void run()  
        {  
            if (!m_acceptor.is_open()) {  
                return;  
            }  
            doAccept();  
        }  
  
        void doAccept()  
        {  
            m_acceptor.async_accept(m_socket,  
                std::bind(  
                    &Listener::onAccept,  
                    shared_from_this(),  
                    std::placeholders::_1));  
        }  
  
        void onAccept(boost::system::error_code errorCode)  
        {  
            if (errorCode)  
            {  
                logError(errorCode, "accept");  
            }  
            else  
            {  
                // Create the session and run it  
                std::make_shared<Session>(  
                    std::move(m_socket),  
                    m_baseResourceName)->run();  
            }  
  
            // Accept another connection  
            doAccept();  
        }  
    };  
} // namespace Http  
```  
  
**Session:**  
```  
// Used namespaces  
using tcp = boost::asio::ip::tcp;           // from <boost/asio/ip/tcp.hpp>  
namespace boostHttp = boost::beast::http;   // from <boost/beast/http.hpp>  
  
  
namespace Http  
{  
    class Session : public std::enable_shared_from_this<Session>  
    {  
    private:  
        // This is the C++11 equivalent of a generic lambda.  
        // The function object is used to send an HTTP message.  
        struct send_lambda  
        {  
            Session& self_;  
  
            explicit send_lambda(Session& self) : self_(self) {}  
  
            template<bool isRequest, class Body, class Fields>  
            void operator()(boostHttp::message<isRequest, Body, Fields>&& msg) const  
            {  
                // The lifetime of the message has to extend  
                // for the duration of the async operation so  
                // we use a shared_ptr to manage it.  
                auto sp = std::make_shared<boostHttp::message<isRequest, Body, Fields>>(std::move(msg));  
  
                // Store a type-erased version of the shared  
                // pointer in the class to keep it alive.  
                self_.res_ = sp;  
  
                // Write the response  
                boostHttp::async_write(self_.socket_, *sp,  
                    boost::asio::bind_executor(  
                        self_.strand_, std::bind(  
                            &Session::onWrite,  
                            self_.shared_from_this(),  
                            std::placeholders::_1,  
                            std::placeholders::_2,  
                            sp->need_eof())));  
            }  
        };  
  
        // Report a failure  
        void logError(boost::system::error_code errorCode, char const* what)  
        {  
            LBC_ERROR("{}: {}", what, errorCode.message());  
        }  
  
        tcp::socket socket_;  
        boost::asio::strand<boost::asio::any_io_executor> strand_;  
        boost::beast::flat_buffer buffer_;  
        std::string const& baseResourceName_;  
        boostHttp::request<boostHttp::string_body> req_;  
        std::shared_ptr<void> res_;  
        send_lambda lambda_;  
  
    public:  
        // Take ownership of the socket  
        explicit Session(tcp::socket socket, std::string const& docRoot)  
            : socket_(std::move(socket))  
            , strand_(socket_.get_executor())  
            , baseResourceName_(docRoot)  
            , lambda_(*this)  
        {}  
  
        // Start the asynchronous operation  
        void run()  
        {  
            doRead();  
        }  
  
        void doRead()  
        {  
            // Make the request empty before reading,  
            // otherwise the operation behavior is undefined.  
            req_ = {};  
  
            // Read a request  
            boostHttp::async_read(socket_, buffer_, req_,  
                boost::asio::bind_executor(  
                    strand_, std::bind(  
                        &Session::onRead,  
                        shared_from_this(),  
                        std::placeholders::_1,  
                        std::placeholders::_2)));  
        }  
  
        void onRead(boost::system::error_code errorCode, std::size_t transferredBytes)  
        {  
            boost::ignore_unused(transferredBytes);  
  
            // This means they closed the connection  
            if (errorCode == boostHttp::error::end_of_stream)  
            {  
                return doClose();  
            }  
  
            if (errorCode) {  
                return logError(errorCode, "*** read");  // Error is here  
            }  
  
            // Some stuff here to manage request  
  
        }  
  
        void onWrite(boost::system::error_code ec, std::size_t transferredBytes, bool close)  
        {  
            boost::ignore_unused(transferredBytes);  
  
            if (ec)  
            {  
                return logError(ec, "write");  
            }  
  
            if (close)  
            {  
                // This means we should close the connection, usually because  
                // the response indicated the "Connection: close" semantic.  
                return doClose();  
            }  
  
            // We're done with the response so delete it  
            res_ = nullptr;  
  
            // Read another request  
            doRead();  
        }  
  
        void doClose()  
        {  
            // Send a TCP shutdown  
            boost::system::error_code ec;  
            socket_.shutdown(tcp::socket::shutdown_send, ec);  
  
            // At this point the connection is closed gracefully  
        }  
    };  
} // namespace Http  
```  
  
The service is launched as follow:  
```  
Service::ServerService serverService;  
serverService.startServer("127.0.0.1", 8080, "service_name", 5);  
```  
  
I saw in the boost documentation that the default limit is 1MB. I tried some examples found on the internet to implement a parser and change the body limit but when I send a payload, I get the following error "Unknown HTTP request" ! I hope someone can help me solve this problem (without to have to rewrite all).  
Thank you in advance for your answers.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-02-17 13:17:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2641#issuecomment-1434640019  

There's a [body_limit](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__basic_parser/body_limit.html) function on the parser.  
  
You'll need to use [this](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__async_read/overload1.html) overload of async_read

---

## Comment 2

> Username: thm-development  
> Created at: 2023-02-20 06:26:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2641#issuecomment-1436401370  

Thank you for your answer. I already tried to add a parser and to use this overload of async_read but when I do that the server stops to receive requests and the client get "Unknown Http request" As I said I've no experience with boost and I don't know how to modify the existing code to add a parser !

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-03-06 00:11:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2641#issuecomment-1455258737  

You can't use a websocket on multiple thread. Either only have one thread call `io_context` or use a strand in the websocket.

---

## Comment 4

> Username: ashtum  
> Created at: 2023-08-19 05:56:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2641#issuecomment-1684842332  

@thm-development Is this still open?

---

## Comment 5

> Username: thm-development  
> Created at: 2023-08-19 07:32:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2641#issuecomment-1684880172  

@ashtum No, I've solved my problem. Thx.

---

## Comment 6

> Username: thm-development  
> Created at: 2023-08-19 07:33:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2641#issuecomment-1684880461  

Thanks to all for your answers
