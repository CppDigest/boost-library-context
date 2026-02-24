# #2675 - Large Binary body in http response object. [Closed]

> Username: jshanaj  
> Created at: 2023-05-01 16:50:16 UTC  
> Updated at: 2024-01-02 13:13:39 UTC  
> Closed at: 2024-01-02 13:13:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2675  

Most the examples use strings but I am pulling a png image from a server.  
The transfer goes well and returns bytes_transfered of around 49K, but when I try to pass that to a function in the callback to hav it draw on a panel I cannot seem to get the incantation correct.  
  
if I use   
     if (pcb_)  
            pcb_((uint8_t*)buffers_front(res_.body().data()).data(), beast::buffers_front(res_.body().data()).size());  
  
I get about 512 bytes of the first buffer. Recognized as a png buf severly cut off.  
  
if I use   
 if (pcb_)  
        pcb_((uint8_t*)beast::buffers_front(buffer_.data()).data(), beast::buffers_front(buffer_.data()).size());  
  
I seem to get the last buffer of again 512 bytes. The png header tag is missing and it throws not a png error. So it seems to be getting the last buffer.  
  
The bytes_transfered is the overall of 49K but How do I get a pointer and size to a contiguous buffer to pass to the graphics lib?  
  
Boost 1.76 with provided boost beast on VS2019

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-05-01 16:59:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2675#issuecomment-1529950379  

What is the full type of your `request` message object?

---

## Comment 2

> Username: jshanaj  
> Created at: 2023-05-01 17:08:57 UTC  
> Updated at: 2023-05-01 17:10:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2675#issuecomment-1529963381  

This should look familiar: sample app with change to http::dynamic_body  
  
```  
// Report a failure  
void  
fail(beast::error_code ec, char const* what)  
{  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Performs an HTTP GET and prints the response  
class session : public std::enable_shared_from_this<session>  
{  
    tcp::resolver resolver_;  
    beast::tcp_stream stream_;  
    beast::flat_buffer buffer_; // (Must persist between reads)  
    http::request<http::empty_body> req_;  
    http::response<http::dynamic_body> res_;  
  
    PayloadCB pcb_;  
  
public:  
    // Objects are constructed with a strand to  
    // ensure that handlers do not execute concurrently.  
    explicit  
        session(net::io_context& ioc, PayloadCB payloadCB)  
        : resolver_(net::make_strand(ioc))  
        , stream_(net::make_strand(ioc))  
        , pcb_(payloadCB)  
    {  
    }  
```  
  
  
Am I missing what dynamic_body is all about? Is it about incremental callback and not dynamic size?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-05-01 17:27:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2675#issuecomment-1529981881  

dynamic_buffer (what dynamic_body uses) is implemented using one or more buffers. So looking at the front is not good enough. Wait until the response message is finished, and then inspect ALL the elements returned by `data()`. Alternatively you can use a string body and when the download completes it will all be in one buffer in memory (in the string).

---

## Comment 4

> Username: jshanaj  
> Created at: 2023-05-01 17:38:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2675#issuecomment-1529992155  

I saw the multi_buffer under-pinnings. I have minimal asio experiance (using it in one way only in small chunks anyway), but I thought asio had support for scatter gather. ( I suppose only through it's own functions for send and receive)  
  
Would the copy() access the entire buffer as a contiguous set of data? Is there a string_view like way. I know the GUI lib(wxWidgets) is going to copy the pixel data into it's image struct for the OS converting to BGR, but I'd like to avoid an unnecessary copy.

---

## Comment 5

> Username: jshanaj  
> Created at: 2023-05-01 21:08:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2675#issuecomment-1530274455  

While I did the following to make it work, it just feels wrong, like there is a better way (or 2 or 3, it is c++)   
  
` std::vector<uint8_t> temp;  
        temp.reserve(bytes_transferred);  
        for (auto const buffer : beast::buffers_range(res_.body().data()))  
        {  
            temp.insert(std::end(temp), (uint8_t*)buffer.data(), (uint8_t*)buffer.data() + buffer.size());  
        }  
          
        
        if (pcb_)  
            pcb_(temp.data(), temp.size());  
`  
I am open to education.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2023-05-02 00:06:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2675#issuecomment-1530628156  

Yes well you need to take a moment to understand how buffer sequences and dynamic buffers work:  
  
https://www.boost.org/doc/libs/1_62_0/doc/html/boost_asio/reference/ConstBufferSequence.html  
  
https://www.boost.org/doc/libs/1_62_0/doc/html/boost_asio/reference/MutableBufferSequence.html  
  
https://www.boost.org/doc/libs/1_82_0/doc/html/boost_asio/reference/DynamicBuffer_v1.html

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2023-05-10 23:27:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2675#issuecomment-1542930753  

Did you have any success since looking into buffer sequences?

---

## Comment 8

> Username: jshanaj  
> Created at: 2023-05-11 14:05:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2675#issuecomment-1544049989  

The class receiving the data is not under my control so it needs a raw pointer and a size. I am still using the above code.

---

## Comment 9

> Username: klemens-morgenstern  
> Created at: 2023-05-15 01:27:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2675#issuecomment-1547067841  

I think a flat_buffer should do then.

---

## Comment 10

> Username: klemens-morgenstern  
> Created at: 2023-08-18 07:47:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2675#issuecomment-1683507717  

Did you have any luck with a flat_buffer?

---

## Comment 11

> Username: ashtum  
> Created at: 2023-12-31 08:40:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2675#issuecomment-1872862189  

@jshanaj Is this issue still open?

---

## Comment 12

> Username: jshanaj  
> Created at: 2024-01-02 13:13:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2675#issuecomment-1874010996  

I cannot remember exactly how I got past this but Once I understood the flat buffer better I did get it working. This can be closed

---

## Comment 13

> Username: jshanaj  
> Created at: 2024-01-02 13:13:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2675#issuecomment-1874011216  

meant to close
