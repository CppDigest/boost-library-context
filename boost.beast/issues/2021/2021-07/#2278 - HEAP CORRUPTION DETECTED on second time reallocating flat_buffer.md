# #2278 - HEAP CORRUPTION DETECTED on second time reallocating flat_buffer [Closed]

> Username: mberntsen  
> Created at: 2021-07-05 13:54:22 UTC  
> Updated at: 2021-07-05 18:20:33 UTC  
> Closed at: 2021-07-05 18:20:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2278  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
If you rather keep your project secret, feel free  
to email the author at `<vinnie.falco@gmail.com>`; any  
private correspondence is treated as confidential.  
  
When reporting a bug please include the following:  
  
### Version of Beast  
  
1.76  
  
### Steps necessary to reproduce the problem  
  
make basic_flat_buffer allocate a new buffer twice  
I'm using the websockets sample code from:  
https://www.boost.org/doc/libs/develop/libs/beast/example/websocket/server/async/websocket_server_async.cpp  
I've modified the on_read function:  
  
`on_read(  
      beast::error_code ec,  
      std::size_t bytes_transferred)  
   {  
    boost::ignore_unused(bytes_transferred);  
  
    // This indicates that the session was closed  
    if (ec == websocket::error::closed)  
      return;  
  
    if (ec)  
    {  
      fail(ec, "read");  
      return;  
    }  
  
    // Echo the message  
    string s(beast::buffers_to_string(buffer_.data()));  
    std::cerr << "ws message: " << s << std::endl;;  
  
    // Clear the buffer  
    buffer_.consume(buffer_.size());  
  
    //std::cout << s << std::endl;  
    TApp* app = TApp::GetInstance();  
    json response;  
    try  
    {  
      json j = json::parse(s);  
      bool ret = app->ProcessWSRequest(j, response);  
      if (ret)  
      {  
        auto r = response.dump();   
        //std::cout << buffer_.size() << std::endl;  
        auto const a = buffer_.prepare(r.size());  
        strcpy(reinterpret_cast<char*>(a.data()), r.c_str());  
        buffer_.commit(r.size());  
        ws_.text(ws_.got_text());  
        ws_.async_write(  
          buffer_.data(),  
          //boost::asio::buffer(r),  
          beast::bind_front_handler(  
            &session::on_write,  
            shared_from_this()));  
      }  
      else  
      {  
        do_read();  
      }  
    }  
    catch (const std::exception& ex)  
    {  
      std::cout << "WS data discarded" << ex.what() <<  std::endl;  
      do_read();  
    }  
  }  
`  
  
The message I'm sending back is increasing every time, at the point that the message is 1623 bytes, the flat_buffer is not big enough (capacity at this point: 1636) and allocates a new buffer:  
  
`// allocate a new buffer  
    auto const new_size = (std::min<std::size_t>)(  
        max_,  
        (std::max<std::size_t>)(2 * len, len + n));  
    auto const p = alloc(new_size);  
    if(begin_)  
    {  
        BOOST_ASSERT(p);  
        BOOST_ASSERT(in_);  
        std::memcpy(p, in_, len);  
        alloc_traits::deallocate(  
            this->get(), begin_, capacity());  
    }`  
no problem here, then this happens a second time (capacity now 1623, required: 1798) the flat_buffer reallocates again but a HEAP CORRUPTION DETECTED is generated when   
`alloc_traits::deallocate(this->get(), begin_, capacity());`   
is executed.  
### All relevant compiler information  
  
ms visual studio 2019

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-07-05 14:03:46 UTC  
> Updated at: 2021-07-05 14:04:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2278#issuecomment-874138703  

You're calling `prepare` with `size()`  
  
```  
auto const a = buffer_.prepare(r.size());  
```  
  
But then you're calling `strcpy` which also copies the null terminator. Thus you are writing an extra byte past the end of your buffer. `strcpy` is a dangerous function. This opens you up to a textbook buffer-overrun attack :)  
  
  
See:  
https://www.tallan.com/blog/2019/04/04/exploring-buffer-overflows-in-c-part-two-the-exploit/  
  
Congratulations, you just cost your company three billion dollars and let China hack the network.

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-07-05 14:19:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2278#issuecomment-874150699  

May we go ahead and close issue?

---

## Comment 3

> Username: mberntsen  
> Created at: 2021-07-05 14:58:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2278#issuecomment-874174628  

thanks, the devil is in the details. this ticket can indeed be closed.  
  
> Congratulations, you just cost your company three billion dollars and let China hack the network.  
  
lucky for me it's an application that will not be connected to the internet ;)
