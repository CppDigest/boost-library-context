# #550 - asio async_write sslstream return success but tcpdump not cap package [Closed]

> Username: ZeroOf  
> Created at: 2021-08-26 15:24:39 UTC  
> Updated at: 2021-08-28 15:17:45 UTC  
> Closed at: 2021-08-28 14:02:27 UTC  
> Url: https://github.com/boostorg/boost/issues/550  

with boost1.77 or 1.72, openssl 3.0.0-alpha17, muti thread with strand, i run ssl pingpang test, after a little time, it will stop, then check data, find client don't send data out.  
  
i have test it in wsl2-ubuntu20.04,  ubuntu20.04, and suse12.   
my server code  
```  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <boost/asio.hpp>  
#include <boost/asio/ssl.hpp>  
#include <boost/bind/bind.hpp>  
  
using boost::asio::ip::tcp;  
  
class session : public std::enable_shared_from_this<session> {  
public:  
    session(tcp::socket socket, boost::asio::ssl::context &context)  
        : socket_(std::move(socket), context), m_strand(socket.get_executor()) {  
    }  
  
    void start() {  
        do_handshake();  
    }  
  
private:  
    void do_handshake() {  
        auto self(shared_from_this());  
        socket_.async_handshake(boost::asio::ssl::stream_base::server,  
                                [this, self](const boost::system::error_code &error) {  
                                    if (!error) {  
                                        do_read();  
                                    }  
                                });  
    }  
  
    void do_read() {  
        auto self(shared_from_this());  
  
        socket_.async_read_some(boost::asio::buffer(data_),  
                                [this, self](const boost::system::error_code &ec, std::size_t length) {  
                                    if (!ec) {  
                                        std::cout << "get <";  
                                        std::cout.write(data_, length);  
                                        std::cout << std::endl;  
                                        do_write(length);  
                                    }  
                                });  
    }  
  
    void do_write(std::size_t length) {  
        auto self(shared_from_this());  
        std::cout << "send <";  
        std::cout.write(data_, length);  
        std::cout << std::endl;  
        boost::asio::async_write(socket_, boost::asio::buffer(data_, length),  
                                 [this, self](const boost::system::error_code &ec,  
                                              std::size_t /*length*/) {  
                                     if (!ec) {  
                                         do_read();  
                                     }  
                                 });  
    }  
  
    boost::asio::ssl::stream<tcp::socket> socket_;  
    boost::asio::strand<boost::asio::ip::tcp::socket::executor_type> m_strand;  
    char data_[1024];  
};  
  
class server {  
public:  
    server(boost::asio::io_context &io_context, unsigned short port)  
        : acceptor_(io_context, tcp::endpoint(tcp::v6(), port)),  
          context_(boost::asio::ssl::context::sslv23) {  
        context_.set_options(  
            boost::asio::ssl::context::default_workarounds  
            | boost::asio::ssl::context::no_sslv2  
            | boost::asio::ssl::context::single_dh_use);  
        context_.set_password_callback(std::bind(&server::get_password, this));  
        context_.use_certificate_chain_file("server.pem");  
        context_.use_private_key_file("server.pem", boost::asio::ssl::context::pem);  
        context_.use_tmp_dh_file("dh2048.pem");  
  
        do_accept();  
    }  
  
private:  
    std::string get_password() const {  
        return "test";  
    }  
  
    void do_accept() {  
        acceptor_.async_accept(  
            [this](const boost::system::error_code &error, tcp::socket socket) {  
                if (!error) {  
                    std::make_shared<session>(std::move(socket), context_)->start();  
                }  
  
                do_accept();  
            });  
    }  
  
    tcp::acceptor acceptor_;  
    boost::asio::ssl::context context_;  
};  
  
int main(int argc, char *argv[]) {  
    try {  
        if (argc != 2) {  
            std::cerr << "Usage: server <port>\n";  
            return 1;  
        }  
  
        boost::asio::io_context io_context;  
  
        using namespace std; // For atoi.  
        server s(io_context, atoi(argv[1]));  
  
        io_context.run();  
    }  
    catch (std::exception &e) {  
        std::cerr << "Exception: " << e.what() << "\n";  
    }  
  
    return 0;  
}  
```  
  
my client code  
```  
#include <cstdlib>  
#include <cstring>  
#include <functional>  
#include <iostream>  
#include <boost/asio.hpp>  
#include <boost/asio/ssl.hpp>  
#include <boost/thread.hpp>  
  
using boost::asio::ip::tcp;  
using std::placeholders::_1;  
using std::placeholders::_2;  
  
enum {  
    max_length = 1024  
};  
  
class client {  
public:  
    client(boost::asio::io_context &io_context,  
           boost::asio::ssl::context &context,  
           const tcp::resolver::results_type &endpoints)  
        : socket_(io_context, context), strand_(io_context.get_executor()) {  
        socket_.set_verify_mode(boost::asio::ssl::verify_peer);  
        socket_.set_verify_callback(  
            std::bind(&client::verify_certificate, this, _1, _2));  
  
        connect(endpoints);  
    }  
  
private:  
    bool verify_certificate(bool preverified,  
                            boost::asio::ssl::verify_context &ctx) {  
        char subject_name[256];  
        X509 *cert = X509_STORE_CTX_get_current_cert(ctx.native_handle());  
        X509_NAME_oneline(X509_get_subject_name(cert), subject_name, 256);  
        std::cout << "Verifying " << subject_name << "\n";  
  
        return true;  
    }  
  
    void connect(const tcp::resolver::results_type &endpoints) {  
        boost::asio::async_connect(socket_.lowest_layer(), endpoints,  
                                   [this](const boost::system::error_code &error,  
                                          const tcp::endpoint & /*endpoint*/) {  
                                       if (!error) {  
                                           handshake();  
                                       } else {  
                                           std::cout << "Connect failed: " << error.message() << "\n";  
                                       }  
                                   });  
    }  
  
    void handshake() {  
        socket_.async_handshake(boost::asio::ssl::stream_base::client,  
                                [this](const boost::system::error_code &error) {  
                                    if (!error) {  
                                        send_request("hello ssl");  
                                        boost::asio::post(strand_, std::bind(&client::recv, this));  
                                    } else {  
                                        std::cout << "Handshake failed: " << error.message() << "\n";  
                                    }  
                                });  
    }  
  
    void send_request(const std::string &msg) {  
        boost::asio::async_write(  
            socket_, boost::asio::buffer(msg),  
            [this](const boost::system::error_code &error, std::size_t length) {  
                if (!error) {  
                    std::cout << "send data success, size : " << length << std::endl;  
                } else {  
                    std::cout << "Write failed: " << error.message() << std::endl;  
                }  
            });  
    }  
  
    void recv() {  
        boost::asio::async_read(  
            socket_, buffer_, boost::asio::transfer_exactly(9),  
            boost::asio::bind_executor(  
                strand_, [this](const boost::system::error_code &error, std::size_t length) {  
                    if (!error) {  
                        std::istream buffer(&buffer_);  
                        std::vector<char> msg(length, 0);  
                        buffer.readsome(msg.data(), length);  
                        std::string recvMsg(msg.begin(), msg.end());  
                        std::cout << recvMsg << std::endl;  
                        send_request(recvMsg);  
                        boost::asio::post(strand_, std::bind(&client::recv, this));  
                    } else {  
                        std::cout << "Read failed: " << error.message() << std::endl;  
                    }  
                }));  
    }  
  
    boost::asio::ssl::stream<tcp::socket> socket_;  
    boost::asio::streambuf buffer_;  
    boost::asio::strand<boost::asio::io_context::executor_type> strand_;  
};  
  
int main(int argc, char *argv[]) {  
    try {  
        if (argc != 3) {  
            std::cerr << "Usage: client <host> <port>\n";  
            return 1;  
        }  
  
        boost::asio::io_context io_context;  
  
        tcp::resolver resolver(io_context);  
        auto endpoints = resolver.resolve(argv[1], argv[2]);  
  
        boost::asio::ssl::context ctx(boost::asio::ssl::context::sslv23);  
        ctx.load_verify_file("ca.pem");  
  
        client c(io_context, ctx, endpoints);  
        boost::thread_group threadPool;  
        for (size_t i = 0; i < boost::thread::hardware_concurrency(); ++i) {  
            threadPool.create_thread(boost::bind(&boost::asio::io_context::run, &io_context));  
        }  
        io_context.run();  
    }  
    catch (std::exception &e) {  
        std::cerr << "Exception: " << e.what() << "\n";  
    }  
  
    return 0;  
}  
```  
  
and my cert  
[cert.zip](https://github.com/boostorg/boost/files/7060832/cert.zip)

---

## Comment 1

> Username: ZeroOf  
> Created at: 2021-08-26 15:27:14 UTC  
> Updated at: 2021-08-26 15:28:29 UTC  
> Url: https://github.com/boostorg/boost/issues/550#issuecomment-906512674  

run in single thread seemed all right, if it is system problem, may be single thread test time is not enough.

---

## Comment 2

> Username: ZeroOf  
> Created at: 2021-08-26 15:29:59 UTC  
> Url: https://github.com/boostorg/boost/issues/550#issuecomment-906514949  

and if i add timer to trigger another message send, the blocked message can send out.

---

## Comment 3

> Username: ZeroOf  
> Created at: 2021-08-28 15:17:44 UTC  
> Url: https://github.com/boostorg/boost/issues/550#issuecomment-907640596  

finally proofed this is my code wrong, thx to sehe  
here is solution  
https://stackoverflow.com/questions/68932871/boostasioasync-write-write-sslstream-succuess-but-server-not-get
