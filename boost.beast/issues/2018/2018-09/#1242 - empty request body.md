# #1242 - empty request body [Closed]

> Username: Ludea  
> Created at: 2018-09-06 20:39:25 UTC  
> Updated at: 2018-09-13 19:30:39 UTC  
> Closed at: 2018-09-13 19:30:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1242  

Hello,  
  
I follow your tutorial but I can not read the body of the request. The body is empty.  
I use Postman to do http request and only use GET method.  
  
I don't understand what is wrong  
  
Here my code :  
```  
detect_session::detect_session(tcp::socket socket,  boost::beast::flat_buffer   
buffer,std::shared_ptr<std::string const> const& doc_root)  
        : socket(std::move(socket))  
       , strand(socket.get_executor())  
	, timer(socket.get_executor().context(),  
            (std::chrono::steady_clock::time_point::max)())  
	, doc_root(doc_root)  
{  
}  
detect_session::~detect_session()  
{  
    //dtor  
}  
void detect_session::run()  
{  
if(! strand.running_in_this_thread())  
            return boost::asio::post(boost::asio::bind_executor(strand,std::bind(&detect_session::run, shared_from_this())));  
on_timer({});  
do_read();  
}  
void detect_session::on_timer(boost::system::error_code ec)  
{  
        if(ec && ec != boost::asio::error::operation_aborted)  
           // return fail(ec, "timer");  
        // Verify that the timer really expired since the deadline may have moved.  
        if(timer.expiry() <= std::chrono::steady_clock::now())  
            return do_timeout();  
       // Wait on the timer  
        timer.async_wait(  
            boost::asio::bind_executor(  
                strand,  
                std::bind(  
                    &detect_session::on_timer,  
                    shared_from_this(),  
                    std::placeholders::_1)));  
}  
void detect_session::do_read()  
{  
timer.expires_after(std::chrono::seconds(15));  
req = {};  
 http::async_read(  
            socket,  
            buffer,  
            req,  
            boost::asio::bind_executor(  
                strand,  
                std::bind(&detect_session::on_read, this, std::placeholders::_1)  
		));  
}  
void detect_session::on_read(boost::system::error_code ec)  
{  
// Happens when the timer closes the socket  
       if(ec == boost::asio::error::operation_aborted)  
            return;  
        // See if it is a HTTP session  
	if (req.method() == http::verb::get)  
	{  
	std::cout<< req.body() <<std::endl ;  
	}  
  
}  
void detect_session::do_timeout()  
    {  
        // Closing the socket cancels all outstanding operations. They  
        // will complete with boost::asio::error::operation_aborted  
        boost::system::error_code ec;  
        socket.shutdown(tcp::socket::shutdown_both, ec);  
        socket.close(ec);  
    }  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-09-06 21:54:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-419253820  

The body of a GET request is usually empty. What do you think the body should contain?

---

## Comment 2

> Username: Ludea  
> Created at: 2018-09-07 08:30:03 UTC  
> Updated at: 2018-09-07 08:30:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-419364427  

Yes sorry, I improve my code.  
  
```  
void detect_session::on_read(boost::system::error_code ec)  
{  
// Happens when the timer closes the socket  
       if(ec == boost::asio::error::operation_aborted)  
            return;  
        // See if it is a HTTP session  
	if (req.method() == http::verb::get)  
	{  
	std::cout<< "http session !"<<std::endl ;  
	}  
}  
```  
Nothing is print on console. I try curl / postman /web browser, whereas I can see the connection

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-09-07 13:40:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-419442649  

Try printing the entire message unconditionally so we can see it:  
```  
std::cout << req << std::endl;  
```

---

## Comment 4

> Username: Ludea  
> Created at: 2018-09-07 14:23:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-419455561  

Ok , I can see the entire message ! So my issue is not here...  
I have 2 clients 1 http and 1 tcp.  
I can see http request (for now^^) but not tcp.  
  
```  
void detect_session::on_read(boost::system::error_code ec)  
{  
// Happens when the timer closes the socket  
        if(ec == boost::asio::error::operation_aborted)  
            return;  
        // This means they closed the connection  
        if(ec == http::error::end_of_stream)  
            do_eof();  
        if(ec)  
            return log.fail(ec, "read");  
        // See if it is a HTTP session  
	if (req.method() == http::verb::get)  
	{  
	std::cout<< "http request"<< req <<std::endl ;  
	}  
	else  
	{  
	std::cout<<"tcp socket"<<std::endl;  
    }  
}  
```  
I connect this way :  
```  
    boost::asio::io_context io_context;  
    tcp::resolver resolver(io_context);  
    const tcp::resolver::results_type endpoint = resolver.resolve("192.168.9.129", "4000");  
  
    m_tcp_connection = connection_ptr(new tcp_connection(m_io_context));  
    tcp::socket& sock = m_tcp_connection->socket();  
    boost::asio::async_connect(sock, endpoint,  
            [this](boost::system::error_code ec, tcp::endpoint)  
            {  
              if (!ec)  
              {  
                  m_tcp_connection->async_read(m_message_read, boost::bind(&tcp_client::handle_read, this, boost::asio::placeholders::error) );  
              }  
            });  
```  
I can see the connection, but the "tcp socket" is not printing.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-09-07 14:30:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-419457784  

> I have 2 clients 1 http and 1 tcp.  
  
I don't understand. What is a "tcp client?"

---

## Comment 6

> Username: Ludea  
> Created at: 2018-09-07 14:33:12 UTC  
> Updated at: 2018-09-07 14:33:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-419458519  

a desktop app.  
It is based on boost asio, with a gui  
The http client is a web browser

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-09-07 14:36:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-419459411  

Okay but TCP is not a protocol in the same sense that HTTP is a protocol. When your "TCP client" connects to the port which is expecting to read an HTTP request, what happens?

---

## Comment 8

> Username: Ludea  
> Created at: 2018-09-07 14:42:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-419461387  

I know, http protocol is above tcp protocol into osi system.  
But it is the only way i found to separate them.  
  
This is the tcp_server part which accept both connections  
```  
void tcp_server::wait_for_connection()  
  
{  
	acceptor.async_accept(  
	[this](boost::system::error_code ec, tcp::socket socket)  
        {  
          if (!ec)  
          {  
		std::cout << "accepted ! " << std::endl;  
		std::make_shared<detect_session>(std::move(socket), std::move(buffer), doc_root)->run();  
	  }  
	wait_for_connection();  
	});  
}  
```   
With tcp connection, I only see "accepted" printed.  
I expected "tcp socket" (see above).

---

## Comment 9

> Username: vinniefalco  
> Created at: 2018-09-07 14:52:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-419464630  

Okay but what does your "TCP client" send instead of an HTTP request?

---

## Comment 10

> Username: Ludea  
> Created at: 2018-09-07 14:55:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-419465699  

just this:   
```  
boost::asio::io_context io_context;  
    tcp::resolver resolver(io_context);  
    const tcp::resolver::results_type endpoint = resolver.resolve("192.168.9.129", "4000");  
  
    m_tcp_connection = connection_ptr(new tcp_connection(m_io_context));  
    tcp::socket& sock = m_tcp_connection->socket();  
    boost::asio::async_connect(sock, endpoint,  
            [this](boost::system::error_code ec, tcp::endpoint)  
            {  
              if (!ec)  
              {  
                  m_tcp_connection->async_read(m_message_read, boost::bind(&tcp_client::handle_read, this, boost::asio::placeholders::error) );  
              }  
            });  
```  
nothing else

---

## Comment 11

> Username: Ludea  
> Created at: 2018-09-07 19:03:49 UTC  
> Updated at: 2018-09-07 19:04:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-419535789  

Can I use boost::asio::async_read to read http::request ?

---

## Comment 12

> Username: vinniefalco  
> Created at: 2018-09-07 23:27:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-419591786  

Is that the code for your "TCP client?" Because the first thing it does is attempt to read a request. If the server is also trying to read a request, nothing will happen. The client needs to **send** something first.

---

## Comment 13

> Username: Ludea  
> Created at: 2018-09-08 11:36:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-419635426  

```  
    boost::asio::io_context io_context;  
    tcp::resolver resolver(io_context);  
    const tcp::resolver::results_type endpoint = resolver.resolve("192.168.9.129", "4000");  
  
    m_tcp_connection = connection_ptr(new tcp_connection(m_io_context));  
    tcp::socket& sock = m_tcp_connection->socket();  
    boost::asio::async_connect(sock, endpoint,  
            [this](boost::system::error_code ec, tcp::endpoint)  
            {  
              if (!ec)  
              {  
                 write(QString("Welcome !"));  
              }  
            });  
}  
  
void tcp_client::write(QString msg)  
{  
    message e;  
    e.m_type = message::NEW_MSG;  
    e.m_login = m_login;   
    e.m_message = msg.toStdString();  
    write(e);  
}  
  
void tcp_client::write(message& e)  
{  
        m_tcp_connection->async_write(e,  
            boost::bind(&tcp_client::handle_write, this,  
            boost::asio::placeholders::error)  
            );  
}  
```  
If I do that, the full request message is : `HTTP/1.1`   
And I can not see the message "tcp socket" expected printed into on_read handler

---

## Comment 14

> Username: vinniefalco  
> Created at: 2018-09-08 12:01:22 UTC  
> Updated at: 2018-09-08 12:01:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-419636917  

This doesn't look like Beast code. Your `message` looks like something else. And `m_tcp_connection->async_write` is neither an asio function nor a Beast function. I don't know how to help you with that.

---

## Comment 15

> Username: Ludea  
> Created at: 2018-09-08 16:56:18 UTC  
> Updated at: 2018-09-08 17:02:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-419657420  

I forget to share this :s  
```  
class tcp_connection  
{  
public:  
    tcp_connection(boost::asio::io_context& io_context)	: m_socket(io_context)  
    {  
    }  
  
    boost::asio::ip::tcp::socket& socket()  
    {  
        return m_socket;  
    }  
  
    template <typename T, typename Handler>  
    void async_read(T& t, Handler handler)  
    {  
        // Issue a read operation to read exactly the number of bytes in a header.  
        void (tcp_connection::*f)(  
            const boost::system::error_code&,  
            T&, boost::tuple<Handler>)  
            = &tcp_connection::handle_read_header<T, Handler>;  
        boost::asio::async_read(m_socket, boost::asio::buffer(m_inbound_header),  
            boost::bind(f,  
            this, boost::asio::placeholders::error, boost::ref(t),  
            boost::make_tuple(handler)));  
    }  
  
    /// Handle a completed read of a message header. The handler is passed using  
    /// a tuple since boost::bind seems to have trouble binding a function object  
    /// created using boost::bind as a parameter.  
    template <typename T, typename Handler>  
    void handle_read_header(const boost::system::error_code& e,  
        T& t, boost::tuple<Handler> handler)  
    {  
        if (e)  
        {  
            boost::get<0>(handler)(e);  
        }  
        else  
        {  
            // Determine the length of the serialized data.  
            std::istringstream is(std::string(m_inbound_header, header_length));  
            std::size_t m_inbound_datasize = 0;  
            if (!(is >> std::hex >> m_inbound_datasize))  
            {  
                // Header doesn't seem to be valid. Inform the caller.  
                boost::system::error_code error(boost::asio::error::invalid_argument);  
                boost::get<0>(handler)(error);  
                return;  
            }  
  
            // Start an asynchronous call to receive the data.  
            m_inbound_data.resize(m_inbound_datasize);  
            void (tcp_connection::*f)(  
                const boost::system::error_code&,  
                T&, boost::tuple<Handler>)  
                = &tcp_connection::handle_read_data<T, Handler>;  
            boost::asio::async_read(m_socket, boost::asio::buffer(m_inbound_data),  
                boost::bind(f, this,  
                boost::asio::placeholders::error, boost::ref(t), handler));  
        }  
    }  
  
    /// Handle a completed read of message data.  
    template <typename T, typename Handler>  
    void handle_read_data(const boost::system::error_code& e,  
        T& t, boost::tuple<Handler> handler)  
    {  
        if (e)  
        {  
            boost::get<0>(handler)(e);  
        }  
        else  
        {  
            // Extract the data structure from the data just received.  
            try  
            {  
                std::string archive_data(&m_inbound_data[0], m_inbound_data.size());  
                std::istringstream archive_stream(archive_data);  
                boost::archive::text_iarchive archive(archive_stream);  
                archive >> t;  
            }  
            catch (std::exception& e)  
            {  
                // Unable to decode data.  
                boost::system::error_code error(boost::asio::error::invalid_argument);  
                boost::get<0>(handler)(error);  
                return;  
            }  
  
            // Inform caller that data has been received ok.  
            boost::get<0>(handler)(e);  
        }  
    }  
```  
  
and message.h  
```  
class message  
{  
public:  
  
    void reset()  
    {  
        m_list_string.clear();  
        m_message.clear();  
        m_login.clear();  
    }  
  
    int m_type;  
  
    // Generic datas  
    std::list<std::string>				m_list_string;  
    std::string							m_message;  
    std::string							m_login;  
  
    template<class Archive>  
    void serialize(Archive& ar, const unsigned int version){  
        ar & m_type & m_list_string & m_message & m_login;  
    }  
  
    enum {  
        NEW_MSG = 0,  
        PERSON_LEAVED = 1,  
        PERSON_CONNECTED = 2,  
    };  
};  
```  
Before adding http::read, I used boost::asio::async_read and I reiceved message from my 'tcp client".  
I try to add http request support, so I switch to http::read, but my "tcp_client" don't send http request.  
So may I use boost_asio_async_read to read both "tcp message" and http request ?

---

## Comment 16

> Username: vinniefalco  
> Created at: 2018-09-08 19:49:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-419668454  

> may I use boost_asio_async_read to read both "tcp message" and http request ?  
  
Yes that is possible. Use `async_read_some` to read your data into a **DynamicBuffer** such as a `beast::flat_buffer`. After the read completes, check if the data matches your custom `message` handshake. If yes, then proceed normally.  
  
If no, then it must be an HTTP message, so use `beast::http::async_read` and pass the `flat_buffer` that already contains data. Beast will look to the buffer first for the HTTP message and use whatever is there. If additional data is needed it will then go to the socket.

---

## Comment 17

> Username: Ludea  
> Created at: 2018-09-10 17:29:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-419994572  

```  
boost::beast::flat_buffer buffer;  
boost::asio::async_read(socket, buffer,   
[this] (boost::system::error_code ec, std::size_t)  
{  
if (! ec)   
std::cout<< buffer.data()<<std::endl;  
}  
});  
```  
give : `buffer is not captured `

---

## Comment 18

> Username: vinniefalco  
> Created at: 2018-09-10 20:13:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-420044793  

That is a compilation error, you are mentioning the variable `buffer` but your lambda capture expression does not include the identifier name. Even if you fix it you will have some problems because your variable can go out of scope before the asynchronous operation completes. I suggest you use a synchronous operation, i.e. `read` instead of `async_read`.

---

## Comment 19

> Username: Ludea  
> Created at: 2018-09-11 08:53:20 UTC  
> Updated at: 2018-09-11 08:57:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-420198702  

I switch to boost::asio::read()  
```  
void detect_session::do_read()  
{  
timer.expires_after(std::chrono::seconds(15));  
req = {};  
        boost::asio::read(socket, buffer);  
       std::cout<< boost::beast::buffers(buffer.data()) <<std::endl;  
}  
```  
But the buffer is empty, with both "tcp request" and http request

---

## Comment 20

> Username: Ludea  
> Created at: 2018-09-11 09:33:50 UTC  
> Updated at: 2018-09-11 09:34:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-420210561  

I also try async operation:  
```  
      boost::asio::async_read(  
            socket,  
            buffer,  
            boost::asio::bind_executor(  
                strand,  
                std::bind(&detect_session::on_read, this, std::placeholders::_1)  
		));  
  
void detect_session::on_read(boost::system::error_code ec)  
{  
// Happens when the timer closes the socket  
        if(ec == boost::asio::error::operation_aborted)  
            return;  
        // This means they closed the connection  
        if(ec == http::error::end_of_stream)  
            do_eof();  
        if(ec)  
            return log.fail(ec, "read");  
std::cout<< "data : " << boost::beast::buffers(buffer.data())  <<std::endl;  
}  
```  
Nothing into buffer

---

## Comment 21

> Username: vinniefalco  
> Created at: 2018-09-11 13:11:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-420268887  

I'm not sure how to help you because this looks very much like an Asio problem and not a Beast problem, and also I think you might not be using Asio correctly.

---

## Comment 22

> Username: Ludea  
> Created at: 2018-09-13 14:57:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-421037589  

I fix some issue. But I think I read the wrong socket.  
It gives me `bad file descriptor`  
  
```  
void detect_session::do_read()  
{  
    timer.expires_after(std::chrono::seconds(15));  
	m_tcp_connection = connection_ptr(new tcp_connection(socket.get_executor().context()));  
	m_tcp_connection->async_read(message_read, boost::bind(&detect_session::on_read, this, boost::asio::placeholders::error) );  
}  
  
void detect_session::on_read(boost::system::error_code ec)  
{  
// Happens when the timer closes the socket  
        if(ec == boost::asio::error::operation_aborted)  
            return;  
        // This means they closed the connection  
        if(ec == http::error::end_of_stream)  
            do_eof();  
        if(ec)  
        return log.fail(ec, "read");  
       std::cout<< message_read.m_message<< std::endl;  
          
      //TODO : Handshake message  
  
        // See if it is a HTTP session  
            req = {};  
  
            //http::async_read(socket, buffer, req, boost::asio::bind_executor(strand, std::bind(&detect_session::checkGETVerb, this, std::placeholders::_1) ));  
}  
```  
  
The constructor is like this:  
```  
detect_session::detect_session(tcp::socket socket,  boost::beast::flat_buffer buffer,std::shared_ptr<std::string const> const& doc_root)  
        : socket(std::move(socket))  
        , strand(socket.get_executor())  
	, timer(socket.get_executor().context(),  
            (std::chrono::steady_clock::time_point::max)())  
	, doc_root(doc_root)  
```

---

## Comment 23

> Username: Ludea  
> Created at: 2018-09-13 19:30:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1242#issuecomment-421124990  

I close the issue.  
It is not a Beast related issue.  
Thanks for your help !
