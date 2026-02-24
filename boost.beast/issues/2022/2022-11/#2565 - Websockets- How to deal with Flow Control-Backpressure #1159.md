# #2565 - Websockets: How to deal with Flow Control/Backpressure #1159 [Closed]

> Username: echen333  
> Created at: 2022-11-17 08:04:51 UTC  
> Updated at: 2023-08-18 16:52:59 UTC  
> Closed at: 2023-08-18 16:52:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2565  

How do the boost beast websocket handle flow control?  
  
I saw from https://www.boost.org/doc/libs/1_80_0/libs/beast/doc/html/beast/design_choices.html:  
  
> Leave important decisions, such as allocating memory or managing flow control, to the user.  
  
But how does the user actually deal with flow control? As in, if the user is taking a long time to deal with incoming information, the websocket data is then stored in a buffer, but how could the size of this buffer be accessed?  
  
Say this is my on_read function for my websocket.  
```  
  int count = 0;  
  const auto& on_read2 = [&count](boost::json::object& json, size_t val) {  
    LOG(INFO) << "Read: \n" << PrettyPrint(json);  
    LOG(INFO) << val << "\n";  
    if (count <= 5) {  
      std::this_thread::sleep_for(std::chrono::milliseconds(1000));  
    }  
    count++;  
  };  
```  
  
```  
void Sync(const T& on_read){  
  stream = std::unique_ptr<  
      boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::tcp_stream>>>  
      secure_websocket_;  
  stream->read(buffer_);  
  
}  
```  
  
Websocket streams are using the boost beast implementation with TCP.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-11-17 08:47:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2565#issuecomment-1318288011  

>  if the user is taking a long time to deal with incoming information, the websocket data is then stored in a buffer,  
  
Well no. A Beast Websocket does not buffer incoming data.

---

## Comment 2

> Username: echen333  
> Created at: 2022-11-17 16:25:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2565#issuecomment-1318888767  

> Well no. A Beast Websocket does not buffer incoming data.  
  
How is it dealt with then? From my tests, it seemed like you still get older data sent even if you are slow to process.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-11-17 16:43:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2565#issuecomment-1318911924  

TCP/IP is first in first out, reliable transmission. That means that data is received in exactly the order it is sent, and nothing is lost. You expect websocket and TCP/IP to throw away data if it is not received by the application within a certain amount of time? You have to write code to make that happen if you want that feature.

---

## Comment 4

> Username: echen333  
> Created at: 2022-11-17 16:45:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2565#issuecomment-1318914168  

Thanks for the help. Is there a way to see how many requests the application still has left to process?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-11-17 16:47:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2565#issuecomment-1318917145  

> Is there a way to see how many requests the application still has left to process?  
  
No. I think you might want to learn or brush up a bit on how TCP/IP flow control works. The requests are not "queued up." It is the TCP/IP protocol that slows the rate of transmission when the receiving application is falling behind. This causes the sending application to block (asynchronous requests will take longer to complete). That is what is meant by backpressure.  
  
The book series "TCP/IP Illustrated" is the best reference for getting a deep and comprehensive understanding for how TCP/IP works (it also covers other protocols, which you may choose to ignore if you want).

---

## Comment 6

> Username: echen333  
> Created at: 2022-11-17 16:51:50 UTC  
> Updated at: 2022-11-17 16:52:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2565#issuecomment-1318922799  

I see, thank you. You're saying the server websocket can tell if the client websocket has read the message and adjust sending messages accordingly? So only if the client has read the last message does it send another one?  
  
But how do people normally deal with if the websocket is falling behind? Like if you only want to process the most recent information, and get read of all non-recent information, how would you do that?  
  
Would you need your own setup?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-11-17 17:22:14 UTC  
> Updated at: 2022-11-17 17:22:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2565#issuecomment-1318962711  

> But how do people normally deal with if the websocket is falling behind?  
  
If you want to be able to discard old messages then you need to constantly read and buffer the messages yourself. And then when you process the next buffered message, take the newest one and discard anything that is too old. This ends up effectively disabling or hindering TCP/IP's natural flow control and taking it over yourself.

---

## Comment 8

> Username: echen333  
> Created at: 2022-11-17 17:24:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2565#issuecomment-1318966109  

I see. thanks for the help.   
  
just wondering, any plans to make an overhead buffer class in boost or is the use case too niche?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2022-11-17 17:25:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2565#issuecomment-1318966663  

> any plans to make an overhead buffer class in boost or is the use case too niche  
  
Could be something that appears in a future library (not beast). But that would be years.

---

## Comment 10

> Username: echen333  
> Created at: 2022-11-17 20:21:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2565#issuecomment-1319156377  

I noticed that the [boost asio socket](https://www.boost.org/doc/libs/1_80_0/doc/html/boost_asio/reference/ip__tcp/socket.html) has data members message_peek and message_end_of_record.   
  
I was wondering if I could those to discard until I get a true from message_end_of_record if it's doing what I think it's doing.   
  
My only problem right now is having trouble accessing the underlying ip_tcp socket. Currently, I have:  
```  
std::unique_ptr<  
      boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::tcp_stream>>>  
      secure_websocket_;  
  std::unique_ptr<boost::beast::websocket::stream<boost::beast::tcp_stream>> insecure_websocket_;  
```  
  
and using   
```  
boost::beast::get_lowest_layer(*stream);  
```  
where stream is one of the two types above. I get something of the type   
```  
boost::beast::basic_stream<boost::asio::ip::tcp>  
```  
  
Is there any way to get the underlying boost::asio::ip::tcp protocol type?

---

## Comment 11

> Username: vinniefalco  
> Created at: 2022-11-17 20:56:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2565#issuecomment-1319190351  

No those are not going to help you

---

## Comment 12

> Username: klemens-morgenstern  
> Created at: 2022-11-18 02:47:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2565#issuecomment-1319475901  

`boost::asio::ip::tcp` is the protocol type.

---

## Comment 13

> Username: ashtum  
> Created at: 2023-08-18 08:57:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2565#issuecomment-1683591274  

@echen333  Could you please close the issue if your problem has been resolved?
