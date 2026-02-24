# #2841 - Issue trying to read an "endless" chunked stream [Closed]

> Username: TunaTheCat  
> Created at: 2024-03-21 15:46:59 UTC  
> Updated at: 2024-03-22 11:40:35 UTC  
> Closed at: 2024-03-22 10:12:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2841  

Hello  
  
I am trying to figure this out since days. Maybe it is just my lack of understanding of beast since i am very new to it. Anyways the case is as follows:  
I have a connection to a backend which is streaming measured data (basically big chunk every 100ms) this is a long lived connection. The connection is made via a http get and then there is just data beeing streamed. i hope this is a clear explanation.  
  
i do the usual connection procedure as it is done in the async examples. with std::enable_shared_from_this and then callbacks with beast::bind_front_handler. i connect to the backend send the get and receive the header (transfer encoding: chunked) this works fine. following i give the next part of the code which is interesting:  
```  
void TamerHandler::on_header_read(beast::error_code ec,  
                                      std::size_t bytes_transferred)  
    {  
        if (ec)  
            return fail(ec, "hdr read");  
  
        assert(parser_.is_header_done());  
        std::cout << "\n---\nresponse headers: " << parser_.get().base() << std::endl;  
           
        std::cout << "bytes read " << bytes_transferred << std::endl;  
        std::cout << "buffer size " << buffer_.size() << std::endl;  
        body_buffer.resize(1024);  
        read_body();  
    }  
  
  
    void TamerHandler::read_body()   
    {   
        // Set up the parser to read into this buffer  
        parser_.get().body().data = body_buffer.data();  
        parser_.get().body().size = body_buffer.size();  
  
        async_read_some(stream_, buffer_, parser_,  
                        beast::bind_front_handler(&TamerHandler::on_body_read,  
                                                  shared_from_this()));  
  
    }  
  
    void TamerHandler::on_body_read(beast::error_code ec,  
                                    std::size_t bytes_transferred)  
    {  
        if (ec)  
        {  
            if (ec == http::error::end_of_stream)  
            {  
                stream_.socket().shutdown(tcp::socket::shutdown_both, ec);  
            }  
            return fail(ec, "body read");  
              
        }  
        std::cout << "bytes read " << bytes_transferred << std::endl;  
        std::cout << "buffer size " << buffer_.size() << std::endl;  
          
        if (!std::all_of(body_buffer.begin(), body_buffer.end(),  
                         [](char c) { return c == '\0'; }))  
        {  
            cbor_buff_.insert(cbor_buff_.end(), body_buffer.begin(), body_buffer.begin() +buffer_.size() +  bytes_transferred);  
            auto it = cbor_buff_.begin();  
            while (it != cbor_buff_.end())  
            {  
                using Result = stt::cbor::StreamValidator::Result;  
  
                auto const r = validator_.feed(*it);  
                ++it;  
                switch (r)  
                {  
                    case Result::Incomplete:  
                    {  
                        break;  
                    }  
                    case Result::Complete:  
                    {  
                        std::cout << "got a complete HISTO" << std::endl;  
                        histo_buff_.insert(histo_buff_.end(), cbor_buff_.begin(), it);  
                       //  cbor_decoder_.parse(histo_buff_);  
                        std::cout << "complete histo size "  
                                  << histo_buff_.size() << std::endl;  
                        histo_buff_.clear();  
                        cbor_buff_.erase(cbor_buff_.begin(), it);  
  
                        // Reset iterator since we modified the container.  
                        it = cbor_buff_.begin();  
                    }  
                    break;  
                    case Result::Error:  
                    {  
                        std::cout << "we have an error" << std::endl;  
                        histo_buff_.clear();  
                        return fail(http::error::bad_chunk, "parser error");  
                    }  
                }  
            }  
            histo_buff_.insert(histo_buff_.end(), cbor_buff_.begin(),  
                               cbor_buff_.end());  
            cbor_buff_.erase(cbor_buff_.begin(), it);  
        }  
          
        if (!parser_.is_done())  
        {  
             
            read_body();  
        }  
    }  
```  
  
What happens is that the parser continuously reads 512 bytes. this is seen with the output:  
bytes transferred 512  
buffer size 0  
  
at some point this happens:  
  
bytes read 471  
buffer size 41  
got a complete HISTO  
complete histo size 123032  
  
Apparently right at the moment where the first CBOR object is finished and the next one probably is already being read. The system gets confused.  
  
Do you have any suggestion on how i might be able to solve this ?  
  
thank you very much

---

## Comment 1

> Username: ashtum  
> Created at: 2024-03-22 07:53:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2841#issuecomment-2014530461  

```C++  
cbor_buff_.insert(cbor_buff_.end(), body_buffer.begin(), body_buffer.begin() + buffer_.size() + bytes_transferred);  
```  
In this line, you should use `body_buffer.size() - parser_.get().body().size()` to get the size of the received buffer.  
`buffer_body` updates its size after each read operation, so it shows the remaining buffer.  
  
By the way, I can't understand what your algorithm is, but note that you are updating the buffer pointer and size in the `read_body()` function. This overwrites the received data, which might not be what you expect.

---

## Comment 2

> Username: TunaTheCat  
> Created at: 2024-03-22 08:19:43 UTC  
> Updated at: 2024-03-22 08:30:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2841#issuecomment-2014590225  

The problem is i get an endless stream of data and i need to find complete objects inside it, so if i do not update the buffer pointer i quickly end in the error case "need buffer". also if i use `parser_.get().body().size` i always get the size which is the size of `body_buffer_`.  
  
my question is how can i reliably know the chunk size, is the parser handling that ?  
  
it seems that i get 512 byte chunks all the time and then when i find a first object, then the parser seems to read only a hand full and the rest stays in the buffer_. This then confuses my "object detection". i do not understand what happens there and how i need to handle that case, when the parser_ seemingly only reads a small number of bytes...

---

## Comment 3

> Username: TunaTheCat  
> Created at: 2024-03-22 10:12:38 UTC  
> Updated at: 2024-03-22 11:40:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2841#issuecomment-2014763760  

I have been able to solve the issue. Apparently when a new object starts in the stream, the parser reads the header (size of next chunked object) and does not update the body_buffer that means that i try to read invalid data. i need to reset the body_buffer before going on to read the next object.
