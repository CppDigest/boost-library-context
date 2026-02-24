# #1638 Fix redefinition of function template [Closed]

> Username: wx257osn2  
> Created at: 2019-06-16 21:08:19 UTC  
> Updated at: 2019-06-17 15:30:51 UTC  
> Closed at: 2019-06-16 21:28:58 UTC  
> Url: https://github.com/boostorg/beast/pull/1638  

In MSVC, I've got compile error like below (sorry, I'm Japanese so the error messages are Japanese):  
  
```  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.20.27508\include\boost\beast\ssl\ssl_stream.hpp(663): error C2995: 'void boost::beast::teardown(boost::beast::role_type,boost::beast::ssl_stream<NextLayer> &,boost::system::error_code &)': 関数テンプレートは既に定義されています  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.20.27508\include\boost\beast\ssl\ssl_stream.hpp(655): note: 'boost::beast::teardown' の宣言を確認してください  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.20.27508\include\boost\beast\core\stream_traits.hpp(114): note: コンパイル対象の クラス テンプレート インスタンス化 'boost::beast::ssl_stream<boost::asio::ip::tcp::socket>' のリファレンスを確認してください  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.20.27508\include\boost\beast\websocket\stream.hpp(174): note: コンパイル対象の エイリアス テンプレート インスタンス化 'boost::beast::executor_type<boost::beast::ssl_stream<boost::asio::ip::tcp::socket>>' のリファレンスを確認してください  
1>R:\boost.beast.example\BoostBeastExample\Source.cpp(148): note: コンパイル対象の クラス テンプレート インスタンス化 'boost::beast::websocket::stream<boost::beast::ssl_stream<boost::asio::ip::tcp::socket>,true>' のリファレンスを確認してください  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.20.27508\include\xstring(1670): note: コンパイル対象の クラス テンプレート インスタンス化 'std::basic_string_view<char,std::char_traits<char>>' のリファレンスを確認してください  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.20.27508\include\boost\beast\ssl\ssl_stream.hpp(677): error C2995: 'void boost::beast::async_teardown(boost::beast::role_type,boost::beast::ssl_stream<NextLayer> &,TeardownHandler &&)': 関数テンプレートは既に定義されています  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.20.27508\include\boost\beast\ssl\ssl_stream.hpp(668): note: 'boost::beast::async_teardown' の宣言を確認してください  
```  
  
Well, the messages says that the functions `boost::beast::teardown` and `boost::beast::teardown_async` were redefined despite the functions had already defined.  
It seems that the `friend` functions were redefined when the `ssl_stream` was instantiated more than once with each different type parameters like `boost::beast::ssl_stream<boost::beast::tcp_stream>` and `boost::beast::ssl_stream<boost::asio::ip::tcp::socket>` .  
  
This PR fixes this issue.

---

## Comment 1

> Username: wx257osn2  
> Created_at: 2019-06-16 21:28:58 UTC  
> Updated_at: 2019-06-17 15:30:51 UTC  
> Url: https://github.com/boostorg/beast/pull/1638#issuecomment-502487213  

Oops, it had been already fixed at 1de60a0 ...  
I'll wait for next release.

---
