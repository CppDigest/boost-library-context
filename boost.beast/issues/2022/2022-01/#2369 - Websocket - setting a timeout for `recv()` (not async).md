# #2369 - Websocket - setting a timeout for `recv()` (not async) [Closed]

> Username: flashnuke  
> Created at: 2022-01-03 12:34:14 UTC  
> Updated at: 2022-06-14 17:38:51 UTC  
> Closed at: 2022-06-14 17:38:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2369  

### Version of Beast  
  
1.76.0  
  
  
I've been trying to find a way to set a timeout without converting my entire code to async, and I've come across this code:  
  
`get_lowest_layer(*_ws).set_option(boost::asio::detail::socket_option::integer<SOL_SOCKET, SO_RCVTIMEO>{ 200 });`  
  
and I get the following exception  
 > set_option: Invalid argument  
  
What am I doing wrong?  
  
Here's the relevant part of my code:  
  
net::io_context ioc;  
    ssl::context ctx{ssl::context::tlsv12_client};  
    tcp::resolver resolver{ioc};  
    mCurrentActiveWS = std::make_shared<websocket_instance>(websocket_instance(ioc, ctx));  
    mCurrentActiveWS->secure_prng(mUseSecurePRNG);  
    SSL_set_tlsext_host_name(mCurrentActiveWS->next_layer().native_handle(), mHost.c_str());  
    const boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp> ex_client = resolver.resolve(mHost, mPort);  
    auto ep = net::connect(get_lowest_layer(*mCurrentActiveWS), ex_client);  
    std::string full_host = mHost + ':' + std::to_string(ep.port());  
    mCurrentActiveWS->next_layer().handshake(ssl::stream_base::client);  
    mCurrentActiveWS->handshake(full_host, mAdditionalHandshakeData);  
    get_lowest_layer(*mCurrentActiveWS).set_option(boost::asio::detail::socket_option::integer<SOL_SOCKET, SO_RCVTIMEO>{ 200 });

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-01-03 13:11:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2369#issuecomment-1004081609  

> What am I doing wrong?  
  
First of all, using the private detail interface of Asio.  
  
The websocket protocol includes automatic ping/pong, so setting a receive timeout on the socket itself may not achieve what you want in any case. The client may well be sending you pings but no data frames. In this case your socket won't time out.  
  
If you want an asynchronous timeout in synchronous code, then probably the easiest way to achieve this is to run a thread to handle the timeouts, and close the socket on a timeout.  
The of course you'll have to manage the race conditions with mutexes and maybe a shared state.  
  
At which point using async code with coroutines (either boost stackful coroutines or c++20 stackless coroutines) starts to look a lot cleaner.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-01-03 16:05:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2369#issuecomment-1004192477  

Yeah synchronous websocket is not really there for production use, and timeouts are certainly not going to work the way you want.

---

## Comment 3

> Username: flashnuke  
> Created at: 2022-01-05 15:24:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2369#issuecomment-1005797975  

I resorted to using `async_*` methods and the official timeout settings from the documentation, thanks!
