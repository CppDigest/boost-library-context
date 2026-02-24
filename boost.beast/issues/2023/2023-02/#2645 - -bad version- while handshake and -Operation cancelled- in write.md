# #2645 - "bad version" while handshake and "Operation cancelled" in write [Closed]

> Username: chivas1000  
> Created at: 2023-02-22 03:26:32 UTC  
> Updated at: 2023-02-23 09:02:42 UTC  
> Closed at: 2023-02-23 09:02:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2645  

Hi everyone,  
I've constructed a socket client and server and using it communicate locally  
Building is good, but when running in root privilege(bind() needs root), the socket got bad version while handshake,  
Before I removed the resolver and modified the code with binding local endpoint, it works good.  
But I didn't modify the handshake part.  
and cannot see whats going on in debug mode because it straight go into the write operation.  
is there any wrong with the code?  
any assistance and guidance would be appreciated, Thanks!  
  
or is that I used  
   ` websocket::stream <beast::tcp_stream> ws_;`  
although it is used in original code  
  
  
for simplicity, the error code came in:  
on_handshake()  
on_write()  
  
might be similar:  
https://github.com/boostorg/beast/issues/2195  
  
version: 1.70.0  
  
Code:  
ClientService.cc  
```  
// Client Connect to Server  
void ClientService::Connect(const std::string &host, unsigned int port) {  
    const auto id = (unsigned int)(mpSLAM->GetMap()->mnId);  
  
    mThread = new std::thread([this, host, port, id] {  
        auto const text = "Hello, world!";  
        info("client {} connect to host {} port {}", id, host, port);  
  
        // The io_context is required for all I/O  
        boost::asio::io_context ioc;  
  
        work_guard_type workGuard(ioc.get_executor());  
  
        const std::string  specify_local_address = "0.0.0.0";  
        unsigned int  specify_local_portnumber = 20000;  
  
        info("In connection: client bind to local endpoint host {} port {}", specify_local_address, specify_local_portnumber);  
  
  
        // Launch the asynchronous operation, which would call WebSocket.h  
        auto session = std::make_shared<WS::Client::session>(ioc, specify_local_address.c_str(), reinterpret_cast<unsigned int *>(specify_local_portnumber),  
                                                             std::bind(&ClientService::OnRequest, this, std::placeholders::_1));  
  
  
        this->service = session;  
        session->run(host.c_str(), reinterpret_cast<unsigned int *>(port), text);  
  
        // Run the I/O service. The call will return when  
        // the socket is closed.  
        ioc.run();  
  
    });  
}  
  
void ClientService::SendRequest(const Request &req) {  
    // serialize and send request  
    std::string msg = ORB_SLAM2::toString(req);  
  
    this->service->send(make_shared<std::string>(msg));  
}  
  
  
```  
  
WebSocket.h of client  
  
```  
//The constructor  
  
namespace Client {  
// Sends a WebSocket message and prints the response  
class session : public std::enable_shared_from_this<session> {  
//    we do not need a resolver since itself initialize a connection  
//    tcp::resolver resolver_;  
//    websocket::stream <tcp::socket> ws_;  
    websocket::stream <beast::tcp_stream> ws_;  
//    beast::tcp_stream ws_;  
    beast::flat_buffer buffer_;  
    std::vector<std::shared_ptr<const std::string>> queue;  
    std::string host_;  
    std::uint8_t port_;  
    std::function<void(const std::string&)> on_message;  
    std::string localhost_;  
    std::uint8_t localport_;  
  
public:  
    // Resolver and socket require an io_context  
    explicit  
        session(net::io_context &ioc, char const *localhost, unsigned int *localport, std::function<void(const std::string&)> on_message)  
    : ws_(net::make_strand(ioc)), on_message(std::move(on_message))  
    {  
        localhost_ = localhost;  
  
        std::stringstream str_port_value;  
        str_port_value << localport;  
        str_port_value >> localport_;  
  
        beast::error_code err;  
          
        //Here I've bind the local endpoint  
        beast::get_lowest_layer(ws_).socket().open(boost::asio::ip::tcp::v4(), err);  
        beast::get_lowest_layer(ws_).socket().bind(tcp::endpoint(boost::asio::ip::make_address_v4(localhost_), localport_));  
  
    }  
  
  
    // Start the asynchronous operation  
    void  
    run(  
            char const *host,  
            unsigned int *port,  
            __attribute__((unused)) char const *text) {  
        // Save these for later  
        host_ = host;  
        std::stringstream str_port_value;  
        str_port_value << port;  
        str_port_value >> port_;  
  
        // dropped the resolver  
//        resolver_.async_resolve(  
//                host,  
//                port,  
//                beast::bind_front_handler(  
//                        &session::on_resolve,  
//                        shared_from_this()));  
  
        //construct a tcp::endpoint using ip::address_v4 and port number  
        tcp::endpoint ep(boost::asio::ip::address::from_string(host_.c_str()), port_);  
  
        beast::get_lowest_layer(ws_).expires_after(std::chrono::seconds(30));  
         
        //here just connect to ep without resolver  
        beast::get_lowest_layer(ws_).socket().async_connect(  
                ep,  
                beast::bind_front_handler(  
                        &session::on_connect,  
                        shared_from_this()));  
    }  
  
    void  
//    on_connect(beast::error_code ec, tcp::resolver::results_type::endpoint_type ep) {  
        on_connect(beast::error_code ec)  
        {  
  
        //  get the ep parameter from run() as ep_  
        tcp::endpoint ep_(boost::asio::ip::address::from_string(host_.c_str()), port_);  
  
        if (ec)  
            return fail(ec, "connect");  
  
        // Turn off the timeout on the tcp_stream, because  
        // the websocket stream has its own timeout system.  
        beast::get_lowest_layer(ws_).expires_never();  
  
        // Set suggested timeout settings for the websocket  
        ws_.set_option(  
                websocket::stream_base::timeout::suggested(  
                        beast::role_type::client));  
  
        // output on screen said making a handshake with server  
        std::cout << "Making a handshake with server" << std::endl;  
          
        //where possibly go wrong  
        // Set a decorator to change the User-Agent of the handshake  
        ws_.set_option(websocket::stream_base::decorator(  
                [](websocket::request_type &req) {  
                    req.set(http::field::user_agent,  
                            std::string(BOOST_BEAST_VERSION_STRING) +  
                            " websocket-client-async");  
                }));  
  
        // update the host string. This will provide the value of the  
        // host HTTP header during the websocket handshake  
        // the guide references: https://tools.ietf.org/html/rfc7230#section-5.4  
        host_ += ':' + std::to_string(ep_.port());  
  
  
        // Perform the websocket handshake  
        ws_.async_handshake(host_, "/",  
                            beast::bind_front_handler(  
                                    &session::on_handshake,  
                                    shared_from_this()));  
    }  
  
    void  
    on_handshake(beast::error_code ec) {  
        //here comes the error code  
        if (ec)  
            return fail(ec, "handshake");  
  
        buffer_.consume(buffer_.size());  
        net::post(ws_.get_executor(), beast::bind_front_handler(&session::on_read, shared_from_this(), ec, 5));  
        std::cout << "Handshake successful." << std::endl;  
    }  
  
    void  
    on_write(  
            beast::error_code ec,  
            std::size_t bytes_transferred) {  
        boost::ignore_unused(bytes_transferred);  
         
        //another error code  
        if (ec)  
            return fail(ec, "write");  
  
        queue.erase(queue.begin());  
  
        // send the message if any  
        if (!queue.empty()) {  
            ws_.async_write(net::buffer(*queue.front()),  
                           beast::bind_front_handler(&session::on_write, shared_from_this()));  
        }  
    }  
  
  
  
```  
  
WebSocket.h of server  
```  
public:  
    // Take ownership of the socket  
    explicit  
    session(tcp::socket &&socket, std::shared_ptr<shared_state> state, std::function<void(std::string)> on_message)  
            : ws_(std::move(socket)), state(std::move(state)), on_message(std::move(on_message)) {  
    }  
  
    ~session() {  
        std::cout << "~session()" << std::endl;  
        state->leave(this);  
    }  
  
    // Start the asynchronous operation  
    void  
    run() {  
        // Set suggested timeout settings for the websocket  
        ws_.set_option(  
                websocket::stream_base::timeout::suggested(  
                        beast::role_type::server));  
  
        // Set a decorator to change the Server of the handshake  
        ws_.set_option(websocket::stream_base::decorator(  
                [](websocket::response_type &res) {  
                    res.set(http::field::server,  
                            std::string(BOOST_BEAST_VERSION_STRING) +  
                            " websocket-server-async");  
                }));  
  
        // Accept the websocket handshake  
        ws_.async_accept(  
                beast::bind_front_handler(  
                        &session::on_accept,  
                        shared_from_this()));  
    }  
  
    void  
    on_accept(beast::error_code ec) {  
        if (ec)  
            return fail(ec, "accept");  
  
        state->join(this);  
        // Read a message  
        do_read();  
    }  
```  
  
  
  
running log:  
[11:07:16][3518][I][RegisterRemote:70] Registering remote client  
[11:07:16][3518][I][Register:172] client bind to local endpoint host 0.0.0.0 port 20001  
[11:07:16][3518][I][Register:173] client register to host 0.0.0.0 port 10088  
1 2330  
[11:07:16][3518][I][RegisterRemote:79] Registered client with id: 1 and port: 2330  
[11:07:16][3518][I][RegisterRemote:85] Connecting to the data channel  
[11:07:16][3518][I][RegisterRemote:89] Connected to the data channel  
[11:07:16][3533][I][operator():39] client 1 connect to host 0.0.0.0 port 2330  
[11:07:16][3533][I][operator():54] In connection: client bind to local endpoint host 0.0.0.0 port 20000  
Making a handshake with server  
handshake: bad version  
Gtk-Message: 11:07:16.297: Failed to load module "canberra-gtk-module"  
write: Operation canceled

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-02-22 14:58:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2645#issuecomment-1440199870  

Can you show what is being sent and received on the wire using a packet sniffer?

---

## Comment 2

> Username: chivas1000  
> Created at: 2023-02-23 09:02:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2645#issuecomment-1441405070  

> Can you show what is being sent and received on the wire using a packet sniffer?  
  
Hi and thanks for the response,  
my issue should be the port number using for binding local endpoint cast to uint_8_t, which is just 0-255 thus broken the handshake rules, and the irregular websocket programming.  
  
here is the answer and I corrected my code referencing the answer:  
https://stackoverflow.com/questions/75528739/boost-beast-bad-version-while-handshake-and-operation-cancelled-in-write
