# #2703 - Handling EOF with Https-Sync client [Closed]

> Username: AvangardAA  
> Created at: 2023-07-09 12:04:29 UTC  
> Updated at: 2023-07-11 17:47:57 UTC  
> Closed at: 2023-07-11 17:47:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2703  

Beast version 1.82  
Good afternoon! I have implemented an HTTPS sync client as a class, and it is not very large. Here is the full code of the client. The desired behavior that I want to achieve is as follows:  
  
The client should maintain a continuous connection and repeatedly send requests to a defined target. If the remote host closes the keep-alive connection or if the connection is closed abnormally (which can result in a broken pipe or EOF during write/read operations as i understand), the client should properly reconnect and set up for the next request before exiting the send_request function. My question is:   
how can I reconstruct/reuse and what should I reconstruct/reuse to establish a persistent connection?  
```  
class Https  
{  
public:  
    Https()  
    {  
        load_root_certificates(ctx);  
  
        ctx.set_verify_mode(ssl::verify_peer);  
    }  
  
    void connect()  
    {  
        try  
        {  
            if (!SSL_set_tlsext_host_name(stream.native_handle(), "blank host"))  
            {  
                beast::error_code ec{static_cast<int>(::ERR_get_error()), net::error::get_ssl_category()};  
                throw beast::system_error{ec};  
            }  
            auto results = resolver.resolve("blank host", "443");  
            beast::get_lowest_layer(stream).connect(results);  
            stream.handshake(ssl::stream_base::client);  
            std::cout << "success " << std::endl;  
        }  
        catch (const beast::system_error& e)  
        {  
            std::cerr << "connect sys error" << std::endl;  
            reconnect();  
        }  
        catch (...)  
        {  
            std::cerr << "connect exception" << std::endl;  
        }  
    }  
  
    void send_request(const std::string& target_)  
    {  
        try  
        {  
            http::request<http::string_body> req{http::verb::get, target_, 11};  
            req.set(http::field::host, "blank host");  
            req.keep_alive(true);  
  
            // Send the HTTP request to the remote host  
            http::write(stream, req);  
  
            // This buffer is used for reading and must be persisted  
            beast::flat_buffer buffer;  
  
            // Declare a container to hold the response  
            http::response<http::dynamic_body> res;  
  
            // Receive the HTTP response  
            http::read(stream, buffer, res);  
            if (res.base().at(http::field::connection) == "close")  
            {  
                std::cerr << "keep-alive close" << std::endl;  
                reconnect();  
                return;  
            }  
  
            // Write the message to standard out  
            std::cout << res << std::endl;  
        }  
        catch (const beast::system_error& e)  
        {  
            std::cerr << e.what() << std::endl;  
            reconnect();  
        }  
        catch (...)  
        {  
            std::cerr << "req error" << std::endl;  
        }  
    }  
private:  
    net::io_context ioc;  
    ssl::context ctx{ssl::context::tlsv12_client};  
    tcp::resolver resolver{ioc};  
    beast::ssl_stream<beast::tcp_stream> stream{ioc, ctx};  
  
    void reconnect()  
    {  
        std::this_thread::sleep_for(std::chrono::seconds(1));  
        connect();  
    }  
};  
  
int main(int argc, char** argv)  
{  
    Https client;  
  
    client.connect();  
      
    while (true)  
    {  
        client.send_request("blank endpoint");  
        std::this_thread::sleep_for(std::chrono::seconds(60));  
    }  
  
    return 0;  
}  
```

---

## Comment 1

> Username: AvangardAA  
> Created at: 2023-07-11 17:47:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2703#issuecomment-1631241796  

resolved myself, regards  
![image](https://github.com/boostorg/beast/assets/70914823/8bfb9e31-02bc-41b1-9138-50df98557b4b)
