# #411 - Missing argument? [Open]

> Username: Hentacler  
> Created at: 2023-03-25 02:20:18 UTC  
> Updated at: 2023-03-25 02:20:18 UTC  
> Url: https://github.com/boostorg/asio/issues/411  

Hey. Please check the following line. I believe, we need to pass "args" as the second argument to std::apply().  
Otherwise, I can't read from a channel that has more than one parameter (in addition to boost::system::error_code).  
  
https://github.com/boostorg/asio/blob/46ed570aa36d1a665b0425e26d013e189b26515b/include/boost/asio/experimental/detail/coro_completion_handler.hpp#L124
