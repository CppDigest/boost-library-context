# #1090 - compiler error under advanced_server.cpp: [Closed]

> Username: bnadem  
> Created at: 2018-04-04 07:59:21 UTC  
> Updated at: 2018-04-13 02:31:41 UTC  
> Closed at: 2018-04-13 02:31:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1090  

wenn compile programm advanced_server.cpp bekom error.n  unter version 1.66  
"advanced_server.cpp:254:39: error: no matching function for call to ‘boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >::control_callback(std::_Bind_helper<false, void (websocket_session::*)(boost::beast::websocket::frame_type, boost::basic_string_view<char, std::char_traits<char> >), websocket_session*, const std::_Placeholder<  
1>&, const std::_Placeholder<2>&>::type)’  
                 std::placeholders::_2));"

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-04-04 14:27:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1090#issuecomment-378619288  

Where did you get the advanced_server.cpp file?

---

## Comment 2

> Username: bnadem  
> Created at: 2018-04-04 14:35:16 UTC  
> Updated at: 2018-04-04 14:36:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1090#issuecomment-378622035  

oh thanks fro replay,  
i get it from https://github.com/boostorg/beast/tree/develop/example/advanced/server  
and have compiled from normal Makefile.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-04-04 14:43:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1090#issuecomment-378625068  

You are trying to use the **develop** version of advanced_server.cpp with the Boost 1.66 release, which are two different versions. Please use the advanced_server.cpp which matches the version of Boost you are using:   
https://github.com/boostorg/beast/blob/boost-1.66.0/example/advanced/server/advanced_server.cpp

---

## Comment 4

> Username: bnadem  
> Created at: 2018-04-04 14:57:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1090#issuecomment-378630641  

thanks. for replay es ist work. program run under defined port and docroot (http) , and who is websocket port running!

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-04-13 02:31:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1090#issuecomment-381002717  

It looks like this issue has been resolved so I'll go ahead and close it, feel free to open new issues as needed!
