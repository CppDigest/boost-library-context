# #2251 - Cant capture stream truncated error in SSL Stream [Closed]

> Username: arifsaeed  
> Created at: 2021-06-03 10:31:54 UTC  
> Updated at: 2022-09-24 05:17:38 UTC  
> Closed at: 2022-09-24 05:17:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2251  

### Version of Beast  
  
BOOST_BEAST_VERSION 266  
  
### Steps necessary to reproduce the problem  
  
I believe that I have the same issue highlighted in https://github.com/boostorg/beast/issues/824  
I have an SSL connection, and I get a stream truncated error when the client disconnects unexpectedly(i.e. the client   
browser is closed)  
  
I would like to capture this error so tried to use boost::asio::ssl::error::stream_truncated but this did not work  
Can someone please explain how I can capture the stream_truncated error?  
  
  
    void  
    websocket_session::  
    do_read()  
    {  
        std::cout<<"do_read\n";  
        // Read a message into our buffer  
        ws_.async_read(  
            buffer_,  
            beast::bind_front_handler(  
                &websocket_session::on_read,  
                shared_from_this()));  
    }  
  
    void  
    websocket_session::  
    on_read(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        std::cout<<"on_read\n";  
        boost::ignore_unused(bytes_transferred);  
  
        std::cout<<"error status: "<<ec.message()<<"\n";  
        std::cout<<"ec value " <<ec.value()<<"\n";  
  
        if(ec == boost::asio::ssl::error::stream_truncated){  
            std::cout<<"stream_truncated error captured, close websocket session\n";  
            return;  
  
        }  
}  
  
### All relevant compiler information  
  
I am using Ubuntu 20.04  
GCC 9.3.0  
I have no issues compiling the code

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-06-03 11:10:07 UTC  
> Updated at: 2021-06-03 11:12:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2251#issuecomment-853788406  

Sorry misread the question. Original answer is below.  
  
Which error is being reported for you when the client truncates the websocket stream?  
  
  
----  
  
Hmm. We actually go to some lengths to remove this error in cases where dropping the ssl stream is legal.  
  
In HTTP/1.1, it is legal for the client to truncate the stream if:  
- the message has a known length and has been fully read, or  
- the transport is CHUNKED and the final chunk has been received, or  
-  The message length is zero and the header has been completely read  
  
Beast understands these rules and will report the read as successful in these cases.  
  
If there is no Content-Length or the protocol is HTTP/1.0 then the error is passed to the caller as you would expect.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2022-01-09 04:16:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2251#issuecomment-1008226707  

This issue has been open for a while with no activity, has it been resolved?
