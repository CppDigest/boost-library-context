# #653 - websocket::stream::control_callback is non-const [Closed]

> Username: vinniefalco  
> Created at: 2017-07-15 04:28:18 UTC  
> Updated at: 2017-07-29 07:27:26 UTC  
> Closed at: 2017-07-29 07:27:26 UTC  
> Url: https://github.com/boostorg/beast/issues/653  

Use `std::ref` to initialize the `std::function` to avoid allocation. Doc will need to reflect that passing temporaries will not work.
