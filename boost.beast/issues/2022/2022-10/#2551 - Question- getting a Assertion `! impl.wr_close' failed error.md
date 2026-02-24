# #2551 - Question: getting a Assertion `! impl.wr_close' failed error. [Closed]

> Username: marketfker  
> Created at: 2022-10-25 09:24:55 UTC  
> Updated at: 2022-11-08 03:19:47 UTC  
> Closed at: 2022-11-08 03:19:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2551  

First time writing websocket and I was trying to test the reconnect of a websocket, it was working for a few times but after a while it returns this error.  
  
/usr/local/include/boost/beast/websocket/impl/read.hpp:1143: std::size_t boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::read_some(const MutableBufferSequence&, boost::beast::error_code&) [with MutableBufferSequence = boost::asio::mutable_buffer; NextLayer = boost::beast::ssl_stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >; bool deflateSupported = true; std::size_t = long unsigned int; boost::beast::error_code = boost::system::error_code]: Assertion `! impl.wr_close' failed.  
  
I have a thread that checks and print outs the message from the websocket  
```  
  try {  
        while (true) {  
            ws->read(buffer);  
            std::string a = beast::buffers_to_string(buffer.data());  
            std::cout<< a <<std::endl;  
            rapidjson::Document d;  
            d.Parse(a.c_str());  
            buffer.clear();  
            if (d.HasParseError()) {  
                Message M(MessageType::m_Reject);  
            }  
           ...  
 catch (...) { }  
```  
and I have another thread that checks the ping pong message, it close and reconnects when did not receive pong.   
  
```  
    ws->control_callback(  
    [&](  
        boost::beast::websocket::frame_type kind,  
        boost::beast::string_view  
    ){  
        if(kind == boost::beast::websocket::frame_type::pong){  
            std::cout << "pong!!\n";  
            // t_pong = std::chrono::system_clock::now().time_since_epoch() / std::chrono::seconds(1);  
        }  
    });  
while (true) {  
        t_ping = std::chrono::system_clock::now().time_since_epoch() / std::chrono::seconds(1);  
        ws->ping(websocket::ping_data(payload));  
        std::this_thread::sleep_for(std::chrono::seconds(10));  
        if (t_pong >= t_ping) {   
        }  
        else {  
            try {  
                ws->close(websocket::close_code::normal);  
            }  
            catch (...) {  
                std::this_thread::sleep_for(std::chrono::seconds(1));  
            }  
            ...\ connecting to the websocket by again in here by emplace as seen in previous discussions  
            break;  
        }  
```  
I thought I would be able to catch the error but it still came from time to time. Is there anything that I did wrong? Thanks

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-10-25 12:14:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2551#issuecomment-1290449053  

Are you using the websocket from multiple threads?

---

## Comment 2

> Username: marketfker  
> Created at: 2022-10-26 02:46:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2551#issuecomment-1291423553  

Yes Im having a thread for reading the message (code on the top) and a thread to check ping and restart it (bottom code).

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2022-10-27 03:12:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2551#issuecomment-1292910747  

That doesn't work. You should use the async interfaces instead.

---

## Comment 4

> Username: marketfker  
> Created at: 2022-11-03 10:30:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2551#issuecomment-1301902220  

Do you mean I cant do multi threading for websocket or it just does not work when try to reconnect? Sorry I tried looking at async but it has not make sense to me yet.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2022-11-05 05:09:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2551#issuecomment-1304408702  

You can't use a websocket on multiple threads period.

---

## Comment 6

> Username: marketfker  
> Created at: 2022-11-08 03:19:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2551#issuecomment-1306571905  

thanks
