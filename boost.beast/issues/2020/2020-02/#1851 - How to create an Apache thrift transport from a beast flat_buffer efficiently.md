# #1851 - How to create an Apache thrift transport from a beast flat_buffer efficiently? [Closed]

> Username: emmenlau  
> Created at: 2020-02-19 16:35:50 UTC  
> Updated at: 2020-02-20 10:53:01 UTC  
> Closed at: 2020-02-20 09:28:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1851  

This is not a bug or problem, but rather a kind request for help in an implementation. I don't understand `boost::beast::flat_buffer` well enough and hope that someone can point me in the right direction. If the question is too far off-topic, please just close it and I will ask on Stack Overflow instead.  
  
I've used boost::beast successfully to create an Apache thrift RPC server with a binary transport over websockets. Thanks to the absolutely excellent [advanced_server_flex.cpp](https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp) its possible to use the javascript thrift client from the browser, together with a boost::beast http/websocket server. This all was impressively simple. But now I'm slightly stuck with a performance issue:  
  
The `class websocket_session` from [advanced_server_flex.cpp](https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp) provides the message as a `boost::beast::flat_buffer buffer_` and expects the response in the same buffer. Apache thrift can construct an input and output transport in memory from existing data: https://github.com/apache/thrift/blob/master/lib/cpp/src/thrift/transport/TBufferTransports.h#L481. This *almost* goes together but I do not get it to work. Is it possible to get an `uint8_t` pointer to the incoming message of `buffer_`, ensure `buffer_` has sufficient storage for the outgoing message, get an `uint8_t` pointer to that outgoing message, and keep both pointers valid for as long as thrift will consume the input and create the output?  
  
### Version of Beast  
```  
#define BOOST_BEAST_VERSION 277  
```  
  
### Steps necessary to reproduce the problem  
  
```  
    // What I currently do is to (slowly) copy the memory to and from thrift. This works  
    // but incurs a huge overhead from creating multiple copies of the message.  
    const std::string vMessage = boost::beast::buffers_to_string(buffer_.cdata());  
    buffer_.consume(buffer_.size());  
    buffer_.reserve(11 * 1024 * 1024);  
  
    std::shared_ptr<::apache::thrift::transport::TTransport> vInputTransport = aThriftProtocol->getInputTransport();  
  
    // write the received message from beast to thrift  
    vInputTransport->write((const uint8_t*)(aMessage.c_str()), aMessage.size());  
    // flush the thrift transport to be sure the message is fully received  
    vInputTransport->flush();  
  
    // have the thrift processor process the message and react to it  
    aThriftProcessor->process(aThriftProtocol, NULL);  
  
    // ask thrift for the size of the response  
    std::shared_ptr<::apache::thrift::transport::TTransport> vOutputTransport = aThriftProtocol->getOutputTransport();  
    uint32_t vNumBytes = 1;  
    vOutputTransport->borrow(nullptr, &vNumBytes);  
  
    // get the response from thrift as a std::string  
    std::string vResult(vNumBytes, '\0');  
    vOutputTransport->read((uint8_t*)(&vResult[0]), vNumBytes);  
  
    // pass the string back to the beast buffer:  
    boost::beast::flat_buffer::mutable_buffers_type vBuffer = buffer_.prepare(vReturn.size());  
    uint8_t* vBufferPtr = static_cast<uint8_t*>(vBuffer.data());  
    std::copy(vReturn.begin(), vReturn.end(), vBufferPtr);  
    buffer_.commit(vReturn.size());  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-02-19 16:38:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1851#issuecomment-588315906  

Use the buffer-oriented WebSocket interfaces instead of the flat buffer, and you can send the string directly:  
https://www.boost.org/doc/libs/1_72_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_read_some/overload2.html  
https://www.boost.org/doc/libs/1_72_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_write_some.html

---

## Comment 2

> Username: emmenlau  
> Created at: 2020-02-19 16:39:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1851#issuecomment-588316330  

Aaah I will take a look at that! Thanks a lot @vinniefalco !

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-02-19 17:18:07 UTC  
> Updated at: 2020-02-19 17:18:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1851#issuecomment-588337024  

I think you can use either a flat_buffer or a multi_buffer and take advantage of the fact that each  
memory region in the buffer contains continugous bytes. This would mean no copying and no need to construct a string:  
```  
void handle_data(boost::beast::multi_buffer& buffer, ::apache::thrift::transport::TTransport& transport)  
{  
    auto buffers = buffer.data();  
    auto ifirst = buffers.begin();  
    auto ilast = buffers.end();  
    while(ifirst != ilast)  
    {  
        auto data = *ifirst++;  
        transport.write(reinterpret_cast<const uint8_t*>(data.data()), data.size());  
    }  
    transport.flush();  
    buffer.consume(buffer.size());  
    buffer.reserve(11 * 1024 * 1024);  
  
}  
  
void handle_data(boost::beast::flat_buffer& buffer, ::apache::thrift::transport::TTransport& transport)  
{  
    auto data = buffer.data();  
    transport.write(reinterpret_cast<const uint8_t*>(data.data()), data.size());  
    transport.flush();  
  
    buffer.consume(buffer.size());  
    buffer.reserve(11 * 1024 * 1024);  
}  
  
```  
  
https://godbolt.org/z/oybcjF

---

## Comment 4

> Username: emmenlau  
> Created at: 2020-02-20 08:56:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1851#issuecomment-588795829  

Guys, you are awesome! I'll report back in a few hours but you pointed me towards a pretty decent implementation!

---

## Comment 5

> Username: emmenlau  
> Created at: 2020-02-20 09:28:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1851#issuecomment-588840502  

Ok, so I checked both your helpful pointers and could gather enough information to avoid any unnecessary memory copies (or so I hope).  
  
To get the data from boost::beast to Apach thrift, @madmongo1 's comment with the `flat_buffer`  pointer to contiguous memory was very helpful. thrift can wrap a shallow copy into a memory transport. I'm under the impression that this should be save and sound, and avoids any copies:  
```cpp  
        boost::beast::flat_buffer buffer_;  
        // receive websocket message with beast here...  
        [...]  
  
        // Construct a temporary in-memory-transport as a shallow copy of the  
        // input boost::beast::flat_buffer data, to avoid copying the data  
        boost::beast::flat_buffer::mutable_data_type vBufferData = buffer_.data();  
        std::shared_ptr<::apache::thrift::transport::TTransport> vInputTransport;  
        vInputTransport = std::make_shared<::apache::thrift::transport::TMemoryBuffer>(reinterpret_cast<uint8_t*>(vBufferData.data()), vBufferData.size());  
        std::shared_ptr<::apache::thrift::protocol::TProtocol> vInputProtocol = mThriftProtocolFactory->getProtocol(vInputTransport);  
  
        mThriftProcessor->process(vInputProtocol, ...);  
```  
  
To get the response back to the websocket, everything was a bit more complicated. This is because only the thrift processor knows how much memory is required for the response, so we can not wrap the transport around the `flat_buffer`. In this case I wrap a shallow `asio::const_buffer` buffer around the thrift transport:  
```cpp  
        std::shared_ptr<::apache::thrift::transport::TTransport> mOutputTransport;  
        mOutputTransport = std::make_shared<::apache::thrift::transport::TMemoryBuffer>();  
  
        std::shared_ptr<::apache::thrift::protocol::TProtocol> mOutputProtocol;  
        mOutputProtocol = mThriftProtocolFactory->getProtocol(mOutputTransport);  
  
        mThriftProcessor->process(vInputProtocol, mOutputProtocol, ...);  
  
        uint32_t vThriftProcessorOutputSize = 1;  
        const uint8_t* vThriftProcessorOutputPtr = mOutputTransport->borrow(nullptr, &vThriftProcessorOutputSize);  
        std::shared_ptr<::boost::asio::const_buffer> mOutputBuffer;  
        mOutputBuffer = std::make_shared<::boost::asio::const_buffer>(vThriftProcessorOutputPtr, vThriftProcessorOutputSize);  
  
        derived().ws().async_write(*mOutputBuffer, ...);  
```  
  
The `shared_ptr`'s are used to ensure the buffers stay valid until `on_write()` where the buffers are consumed.  
  
If you spot any flaw's I would very much appreciate feedback, because I'm pretty new to this. But thanks again for the excellent library and the great support!

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-02-20 10:19:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1851#issuecomment-588906956  

Hi @emmenlau, The receive side looks fine. The use of make_shared on the output side is concerning.  
  
`boost::asio::const_buffer` does not own memoty - it merely references a bounded region of buffer memory.  
  
Firstly, this means that you can simply copy it, with no need to 'keep it alive' witht a shared_ptr.  
```  
        uint32_t vThriftProcessorOutputSize = 1;  
        const uint8_t* vThriftProcessorOutputPtr = mOutputTransport->borrow(nullptr, &vThriftProcessorOutputSize);  
        ::boost::asio::const_buffer mOutputBuffer(  
            vThriftProcessorOutputPtr,   
            vThriftProcessorOutputSize);  
  
        derived().ws().async_write(mOutputBuffer, ...);  // cheap copy is fine  
```  
  
Secondly, the memory buffer that it is referring to must remain stable for the duration of the async_send operation.  
  
Can you guarantee that the pointer returned by `borrow` will point to data which will not be mutated or moved during the async_write?

---

## Comment 7

> Username: emmenlau  
> Created at: 2020-02-20 10:53:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1851#issuecomment-588952747  

I will change my code to simply copy the `boost::asio::const_buffer`, this will make the design cleaner!  
  
For the pointer returned by `borrow()` I can ensure that it remains valid until `async_write()` calls the post-write-handler. Sorry that I did not show this in the code! Contrary to how its displayed in my above code snippet, I've used a class member `shared_ptr` for the output `::apache::thrift::transport::TTransport`. This transport consumes the buffer only after `async_write()`.  
  
Thanks a lot @madmongo1 for the review! Its very much appreciated!
