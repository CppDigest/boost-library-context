# #1688 - Exception in basic_stream destructor [Closed]

> Username: altbog  
> Created at: 2019-08-27 15:08:41 UTC  
> Updated at: 2019-08-29 21:41:59 UTC  
> Closed at: 2019-08-29 21:41:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1688  

The current implementation of basic_stream can throw exceptions from the destructor. It calls impl_->close() as below:  
  
`  
template<class Protocol, class Executor, class RatePolicy>  
basic_stream<Protocol, Executor, RatePolicy>::~basic_stream()  
{  
    // the shared object can outlive *this,  
    // cancel any operations so the shared  
    // object is destroyed as soon as possible.  
    impl_->close();  
}  
`  
  
And the close(), in turn, calls socket.close(). This ASIO function can throw exceptions:  
  
`  
template<class Protocol, class Executor, class RatePolicy>  
void basic_stream<Protocol, Executor, RatePolicy>::impl_type::close()  
{  
    socket.close();  
    timer.cancel();  
}  
`  
  
In my case, I have a double exception because the destructor of data_stream is called during the handling the first exception (connection error). As a result, the application terminated by calling abort().   
The possible solution is to use socket.close(&ec) call in destructor...

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-08-27 15:11:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1688#issuecomment-525346586  

Hmm yes you are right, this should use the other overload.

---

## Comment 2

> Username: altbog  
> Created at: 2019-08-27 15:14:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1688#issuecomment-525348035  

@vinniefalco How fast it could be fixed in the public boost::beast version? What do you think?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-08-27 15:18:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1688#issuecomment-525350097  

You mean in the Boost release? Well, that already shipped. But I can update it in master by today.
