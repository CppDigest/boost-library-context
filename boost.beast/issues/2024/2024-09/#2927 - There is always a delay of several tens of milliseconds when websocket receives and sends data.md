# #2927 - There is always a delay of several tens of milliseconds when websocket receives and sends data [Closed]

> Username: star-0o0  
> Created at: 2024-09-09 08:37:46 UTC  
> Updated at: 2024-09-10 06:03:08 UTC  
> Closed at: 2024-09-10 06:03:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2927  

I developed a WebSocket server using Beast.  
  
### This service always has a delay of 30-40ms when receiving requests from the client, and when the server sends data to the client, there is also a delay of 30-40ms when the client receives the data.  
  
boost version : 1_67_0  
  
Server Code:  
  
`  
class Session;  
extern int on_receive(std::shared_ptr<Session> ssesion);  
extern double get_timestamp();  
  
using tcp = boost::asio::ip::tcp;               // from <boost/asio/ip/tcp.hpp>  
namespace ssl = boost::asio::ssl;               // from <boost/asio/ssl.hpp>  
namespace websocket = boost::beast::websocket;  // from <boost/beast/websocket.hpp>  
  
// Echoes back all received WebSocket messages  
class Session : public std::enable_shared_from_this<Session>  
{  
    tcp::socket m_socket;  
    websocket::stream<ssl::stream<tcp::socket&>> m_ws;  
    boost::asio::strand<boost::asio::io_context::executor_type> m_strand;  
    std::vector<std::shared_ptr<std::string const>> m_queue;  
    TTimer& m_timer;  
    int m_timercallfunid = 0;  
public:  
    boost::beast::multi_buffer m_buffer;  
  
public:  
    // Take ownership of the socket  
    Session(tcp::socket socket, ssl::context &ctx, TTimer& timer): m_socket(std::move(socket)), m_ws(m_socket, ctx),  
		  m_strand(m_ws.get_executor()),  
    {  
    }  
  
    ~Session()  
    {  
        if (m_timercallfunid > 0)  
        {  
            m_timer.RemoveCallFunc(m_timercallfunid);  
        }  
        if (m_gtclient) {  
            delete m_gtclient;  
        }  
        LOG_INFO("session %s quit", m_uuid_token);  
        std::cout << "session " << m_uuid_token <<" quit" << std::endl;  
    }  
  
    size_t   
    read_buf(std::string &data)  
    {     
        data = boost::beast::buffers_to_string(m_buffer.data());  
        // Clear the buffer  
        m_buffer.consume(m_buffer.size());  
        return data.length();  
    }  
  
    size_t   
    write_buf(std::string &data)  
    {  
        size_t n = 0;  
        auto bufs = m_buffer.prepare(data.length());  
        for (auto it: bufs) {  
            memcpy(it.data(), data.c_str() + n, it.size());  
            n += it.size();  
        }  
        m_buffer.commit(n);  
        return n;  
    }  
  
    // Start the asynchronous operation  
    void  
    run()  
    {  
        // Perform the SSL handshake  
		m_ws.next_layer().async_handshake(  
			ssl::stream_base::server,  
			boost::asio::bind_executor(  
				m_strand,  
				std::bind(  
					&Session::on_handshake,  
					shared_from_this(),  
					std::placeholders::_1)));  
	}  
  
    void  
    on_handshake(boost::system::error_code ec)  
    {  
		if (ec) {  
            LOG_ERR("handshake failed: %s", ec.message().c_str());  
			return;  
		}  
  
        // Accept the websocket handshake  
        m_ws.async_accept(  
            boost::asio::bind_executor(  
                m_strand,  
                std::bind(  
                    &Session::on_accept,  
                    shared_from_this(),  
                    std::placeholders::_1)));  
    }  
  
    void  
    on_accept(boost::system::error_code ec)  
    {  
		if (ec) {  
            LOG_ERR("accept failed: %s", ec.message().c_str());  
			return;  
		}  
  
		// Read a message  
        do_read();  
    }  
  
    void  
    do_read()  
    {  
        // Read a message into our buffer  
        m_ws.async_read(  
            m_buffer,  
            boost::asio::bind_executor(  
                m_strand,  
                std::bind(  
                    &Session::on_read,  
                    shared_from_this(),  
                    std::placeholders::_1,  
                    std::placeholders::_2)));  
    }  
  
    void  
    on_read(  
        boost::system::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        // This indicates that the Session was closed  
		if (ec == websocket::error::closed) {  
            LOG_INFO("socket closed");  
			return;  
		}  
        if (bytes_transferred <= 0)  
        {  
            return;  
        }  
          
		if (ec) {  
            LOG_ERR("read failed: %s", ec.message().c_str());  
		}  
        printf("rec: %f \n", get_timestamp());  
        int ret = on_receive(shared_from_this());  
      
        // Clear the buffer  
        m_buffer.consume(m_buffer.size());  
          
        // Do another read  
        do_read();  
    }  
  
    void write_async(const std::string& data)  
    {  
        boost::beast::multi_buffer buffer;  
        size_t n = 0;  
        auto bufs = buffer.prepare(data.length());  
        for (auto it: bufs) {  
            memcpy(it.data(), data.c_str() + n, it.size());  
            n += it.size();  
        }  
        buffer.commit(n);  
        boost::asio::post(m_ws.get_executor(), boost::asio::bind_executor(m_strand, std::bind(&Session::on_async_write,  
            shared_from_this(), std::make_shared<std::string const>(std::move(boost::beast::buffers_to_string(buffer.data()))))));  
    }  
  
    void on_write(boost::system::error_code ec, std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if (ec) {  
            LOG_ERR("write failed: %s", ec.message().c_str());  
		}  
  
        m_queue.erase(m_queue.begin());  
        if (!m_queue.empty()) {  
            m_ws.text(m_ws.got_text());  
            m_ws.async_write(boost::asio::buffer(*m_queue.front()), boost::asio::bind_executor(m_strand, std::bind(&Session::on_write,  
                shared_from_this(), std::placeholders::_1, std::placeholders::_2)));  
        }  
    }  
  
    void on_async_write(std::shared_ptr<std::string const> const& s)   
    {  
        m_queue.push_back(s);  
        if (m_queue.size() > 1) {  
            return;  
        }  
        m_ws.async_write(boost::asio::buffer(*m_queue.front()), boost::asio::bind_executor(m_strand, std::bind(&Session::on_write,  
            shared_from_this(), std::placeholders::_1, std::placeholders::_2)));  
    }  
};  
`

---

## Comment 1

> Username: ashtum  
> Created at: 2024-09-09 08:59:33 UTC  
> Updated at: 2024-09-09 09:13:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2927#issuecomment-2337538365  

Are you testing you application locally? Have you tried setting [ip::tcp::no_delay](https://www.boost.org/doc/libs/1_86_0/doc/html/boost_asio/reference/ip__tcp/no_delay.html)?  
You can use [websocket_server_async.cpp](https://github.com/boostorg/beast/blob/develop/example/websocket/server/async/websocket_server_async.cpp) as a reference if you want to compare it with your code.

---

## Comment 2

> Username: star-0o0  
> Created at: 2024-09-09 09:09:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2927#issuecomment-2337562166  

> Are you testing you application locally? Have you tried to set [ip::tcp::no_delay](https://www.boost.org/doc/libs/1_86_0/doc/html/boost_asio/reference/ip__tcp/no_delay.html)? You can use [websocket_server_async.cpp](https://github.com/boostorg/beast/blob/develop/example/websocket/server/async/websocket_server_async.cpp) as a reference if you want to compare to your code.  
  
Yes，I am testing on the local server. Both the client and server are started locally.

---

## Comment 3

> Username: star-0o0  
> Created at: 2024-09-10 06:03:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2927#issuecomment-2339720393  

set [ip::tcp::no_delay](https://www.boost.org/doc/libs/1_86_0/doc/html/boost_asio/reference/ip__tcp/no_delay.html)   
  
This method can solve the problem.
