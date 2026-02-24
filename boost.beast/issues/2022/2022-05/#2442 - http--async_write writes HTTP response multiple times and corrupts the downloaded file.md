# #2442 - http::async_write writes HTTP response multiple times and corrupts the downloaded file [Closed]

> Username: rohitpai  
> Created at: 2022-05-29 11:00:59 UTC  
> Updated at: 2022-06-14 17:33:25 UTC  
> Closed at: 2022-06-14 17:33:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2442  

I am using boost beast to stream a zip file from my server.   
The file is read from an internal socket and sent over the network.    
I use curl to download the zip file. The whole file to seem to get downloaded but I can't unzip the file and if I compare the original file vs the downloaded file then I see that the downloaded file has extra bytes and this is HTTP response bytes additional getting added to the file.   
![image](https://user-images.githubusercontent.com/1668273/170864396-0267643f-bc63-4151-98cf-38336aab1fec.png)  
If you can see in the middle of the file content there is HTTP response code and header.   
  
  
Here is the code of the server. Please help me identify what's causing the multiple HTTP response code which is going out.   
```  
  
struct DynamicResponse  
{  
    using response_type =  
        boost::beast::http::response<boost::beast::http::buffer_body>;  
  
    std::optional<response_type> bufferResponse;  
    ...........  
}  
  
void doReadStream()  
    {  
        std::size_t bytes = outputBuffer.capacity() - outputBuffer.size();  
  
        this->unixSocket.async_read_some(  
            outputBuffer.prepare(bytes),  
            [this, self(shared_from_this())](  
                const boost::system::error_code& ec, std::size_t bytesRead) {  
                if (ec)  
                {  
                    BMCWEB_LOG_ERROR << "Couldn't read from local peer: " << ec;  
  
                    if (ec != boost::asio::error::eof)  
                    {  
                        BMCWEB_LOG_ERROR << "Couldn't read from local peer: "  
                                         << ec;  
                        this->connection->sendStreamErrorStatus(  
                            boost::beast::http::status::internal_server_error);  
                    }  
                    this->connection->close();  
                    return;  
                }  
  
                outputBuffer.commit(bytesRead);  
                auto streamHandler = [this, bytesRead,  
                                      self(shared_from_this())]() {  
                    this->outputBuffer.consume(bytesRead);  
                    this->doReadStream();  
                };  
                this->connection->sendMessage(  
                    static_cast<char*>(outputBuffer.data().data()),  
                    streamHandler, bytesRead);  
            });  
    }  
  
void doReadStream()  
    {  
        std::size_t bytes = outputBuffer.capacity() - outputBuffer.size();  
  
        this->unixSocket.async_read_some(  
            outputBuffer.prepare(bytes),  
            [this, self(shared_from_this())](  
                const boost::system::error_code& ec, std::size_t bytesRead) {  
                if (ec)  
                {  
                    BMCWEB_LOG_ERROR << "Couldn't read from local peer: " << ec;  
  
                    if (ec != boost::asio::error::eof)  
                    {  
                        BMCWEB_LOG_ERROR << "Couldn't read from local peer: "  
                                         << ec;  
                        this->connection->sendStreamErrorStatus(  
                            boost::beast::http::status::internal_server_error);  
                    }  
                    this->connection->close();  
                    return;  
                }  
  
                outputBuffer.commit(bytesRead);  
                auto streamHandler = [this, bytesRead,  
                                      self(shared_from_this())]() {  
                    this->outputBuffer.consume(bytesRead);  
                    this->doReadStream();  
                };  
                this->connection->sendMessage(  
                    static_cast<char*>(outputBuffer.data().data()),  
                    streamHandler, bytesRead);  
            });  
    }  
  
void sendMessage(char* data, std::function<void()> handler,  
                     size_t size) override  
    {  
        if (size)  
        {  
            this->handlerFunc = handler;  
            streamres.bufferResponse->body().more = true;  
            streamres.bufferResponse->body().data = data;  
            streamres.bufferResponse->body().size = size;  
            doWrite();  
        }  
    }  
  
void doWrite()  
    {  
        boost::beast::http::async_write(  
            adaptor, *streamres.bufferResponse,  
            [this, self(shared_from_this())](boost::beast::error_code ec,  
                                             std::size_t bytesWritten) {  
                BMCWEB_LOG_DEBUG << "async_write wrote " << bytesWritten << ec;  
                  
                if (ec != boost::beast::http::error::need_buffer)  
                {  
                    BMCWEB_LOG_DEBUG << "Error in async_write " << ec;  
                    close();  
                    return;  
                }    
                if(ec ==  boost::beast::http::error::need_buffer)  
                {  
                    ec = {};  
                    (handlerFunc)();  
                }                            
            });  
    }  
  
  
```  
  
The code is from OpenBMC and here is the link to the complete code for your reference   
https://gerrit.openbmc.org/c/openbmc/bmcweb/+/38051/14/http/http_stream.hpp

---

## Comment 1

> Username: sehe  
> Created at: 2022-05-29 11:46:44 UTC  
> Updated at: 2022-05-29 11:48:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2442#issuecomment-1140431647  

That's a bit too much code for me too look at in a second. `buffer_body` can be a bit finicky to get right in terms of buffer manipulations, in my experience. Perhaps you can compare with the documented approach https://www.boost.org/doc/libs/1_77_0/libs/beast/doc/html/beast/using_http/parser_stream_operations/incremental_read.html to spot any difference?  
  
Also, be aware of `file_body` if the intent is to save content to/read from a file: https://stackoverflow.com/a/72418832/85371

---

## Comment 2

> Username: rohitpai  
> Created at: 2022-05-29 13:36:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2442#issuecomment-1140451309  

The implementation is similar to what you have pointed out.   
  
when I use boost::beast::http::async_write and the ec is error::need_buffer, I update the body.data() with new buffer with appropriate size. When I do the second async_write with the body updated I was expecting that only the HTTP updated body will be sent out but what I see is that body is sent along with HTTP response and header section.   
The  repeated HTTP header which is sent again is creating the problem.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-05-29 13:57:02 UTC  
> Updated at: 2022-05-29 13:57:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2442#issuecomment-1140455018  

`beast::http::async_write` can only be used to send the entire message including the body, in a single call. If you are sending a buffer at a time you have to declare a `serializer`  and use that with `beast::http::async_write_some`:  
  
https://www.boost.org/doc/libs/1_79_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_write_some.html

---

## Comment 4

> Username: rohitpai  
> Created at: 2022-05-29 17:32:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2442#issuecomment-1140492627  

I am using  boost::beast::http::response<boost::beast::http::buffer_body>, does not it contain an internal serializer within ?  
This data structure does have the field body().more when its set it returns need_buffer. In my code body().more is set which means I have more data to be sent.   
So as I understand when I get ec as need_buffer I need to update the body data pointer and new body would be sent!. Is my understanding wrong ?  
  
So to be able to send header once and body in multiple calls am I suppose to use only serializer ? Something like below   
    boost::beast::http::response<boost::beast::http::buffer_body> res;  
    boost::beast::http::response_serializer<boost::beast::http::buffer_body, boost::beast::http::fields> sr{res};  
Can you confirm that its not possible to make it work with boost::beast::http::response object ?  
  
thanks for your kind help.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-05-29 17:40:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2442#issuecomment-1140493892  

> So to be able to send header once and body in multiple calls am I suppose to use only serializer ?  
  
Yes
