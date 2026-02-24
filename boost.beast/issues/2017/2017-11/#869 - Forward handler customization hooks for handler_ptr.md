# #869 - Forward handler customization hooks for handler_ptr [Closed]

> Username: djarek  
> Created at: 2017-11-05 20:34:15 UTC  
> Updated at: 2019-02-23 05:41:08 UTC  
> Closed at: 2019-02-23 05:41:08 UTC  
> Url: https://github.com/boostorg/beast/issues/869  

Because handler_ptr is part of the public API, Beast should provide the required member functions and types to forward the allocator and executor associated with the handler.  
- ```executor_type```,  
- ```get_executor()```,  
- ```allocator_type```,  
- ```get_allocator()```,

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-05 20:54:42 UTC  
> Url: https://github.com/boostorg/beast/issues/869#issuecomment-342004679  

`handler_ptr` is neither a composed operation nor a completion handler, I don't think specializations are appropriate for this. But if you can convince me otherwise, I might change it. What is your use-case?

---

## Comment 2

> Username: djarek  
> Created at: 2017-11-06 01:27:21 UTC  
> Url: https://github.com/boostorg/beast/issues/869#issuecomment-342023605  

Most composed operations that perform socket operations have the following structure:  
```  
template <typename Handler, typename Stream>  
struct my_op  
{  
    template <typename DeducedHandler>  
    my_op(DeducedHandler&& dh, Stream& s):  
        handler{std::forward<DeducedHandler>(dh)},  
        stream{s}  
    {  
    }  
  
    void operator()() {}  
  
    using allocator_type = boost::asio::associated_allocator_t<Handler>;  
    using executor_type = boost::asio::associated_executor_t<  
        Handler, decltype(std::declval<Stream&>().get_executor())>;  
  
    allocator_type  
    get_allocator() const noexcept  
    {  
        return boost::asio::get_associated_allocator(handler);  
    }  
  
    executor_type  
    get_executor() const noexcept  
    {  
        return boost::asio::get_associated_executor(  
            handler, stream.get_executor());  
    }  
    Handler handler;  
    Stream& stream;  
    // other stuff  
};  
```  
I don't want to write the hook forwarding boilerplate for **every single operation**.   
I want to be able to simply declare that my operation is a very standard operation, has a handler and a stream (and maybe a bunch of other stuff). So I wrote a small template to do just this, inject the hooks:  
```  
template <typename Handler, typename Stream>  
struct my_op : forward_handler_hooks<my_op<Handler, Stream>>  
{  
    template <typename DeducedHandler>  
    my_op(DeducedHandler&& dh, Stream& s):  
        handler{std::forward<DeducedHandler>(dh)},  
        stream{s}  
    {  
    }  
  
    void operator()() {}  
  
    Handler handler;  
    Stream& stream;  
};  
```  
  
I intend to write a little bit of template logic to detect the presence of the appropriate members, e.g. if the operation does not have a handler member, then expect everything to be stored in some reference type like ```handler_ptr```. Since ```handler_ptr``` is a container for handlers, it makes sense IMO, for it to forward these hooks as well.  
  
TL;DR; Makes life easier if you don't like typing a lot of template stuff.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-11-18 17:54:02 UTC  
> Url: https://github.com/boostorg/beast/issues/869#issuecomment-345458885  

Is this issue still relevant?

---

## Comment 4

> Username: djarek  
> Created at: 2017-11-18 17:55:54 UTC  
> Url: https://github.com/boostorg/beast/issues/869#issuecomment-345459014  

I believe that supplying handler_ptr::get_executor() and handler_ptr::get_allocator() would make it easier to use.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-02-23 05:41:08 UTC  
> Url: https://github.com/boostorg/beast/issues/869#issuecomment-466619411  

`handler_ptr` is deprecated
