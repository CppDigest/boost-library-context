# #6 - Comments arising from previous PR [Closed]

> Username: madmongo1  
> Created at: 2020-05-21 19:44:52 UTC  
> Updated at: 2020-07-06 18:18:35 UTC  
> Closed at: 2020-07-06 18:18:35 UTC  
> Url: https://github.com/boostorg/mysql/issues/6  

I noticed that some async initiating functions had an optional argument after the completion token.  
  
This is something you might want to revisit. The reason is that asio's direction is around auto-selecting the completion token based on the associated executor type of the io_object.  
  
This means that the completion token becomes an optional argument. This argues for all other arguments coming prior.  
  
This may mean a minor inconvenience in terms of having to offer 2 forms of initiating functions, one with the optional arg and one without. But the benefit is a more standardised interface and compatibility with asio/beast.  
  
e.g. notice the DEFAULT template type and final argument:  
  
https://www.boost.org/doc/libs/1_73_0/doc/html/boost_asio/reference/async_read/overload1.html  
  
https://www.boost.org/doc/libs/1_73_0/doc/html/boost_asio/reference/asynchronous_operations.html#boost_asio.reference.asynchronous_operations.default_completion_tokens  
  
```  
Default completion tokens  
Every I/O executor type has an associated default completion token type. This is specified via the default_completion_token trait. This trait may be used in asynchronous operation declarations as follows:  
  
template <  
    typename IoObject,  
    typename CompletionToken =  
      typename default_completion_token<  
        typename IoObject::executor_type  
      >::type  
  >  
auto async_xyz(  
    IoObject& io_object,  
    CompletionToken&& token =  
      typename default_completion_token<  
        typename IoObject::executor_type  
      >::type{}  
  );  
If not specialised, this trait type is void, meaning no default completion token type is available for the given I/O executor.  
  
[Example: The default_completion_token trait is specialised for the use_awaitable completion token so that it may be used as shown in the following example:  
  
auto socket = use_awaitable.as_default_on(tcp::socket(my_context));  
// ...  
co_await socket.async_connect(my_endpoint); // Defaults to use_awaitable.  
```
