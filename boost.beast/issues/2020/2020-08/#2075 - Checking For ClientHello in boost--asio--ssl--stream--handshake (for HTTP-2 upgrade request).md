# #2075 - Checking For ClientHello in boost::asio::ssl::stream::handshake (for HTTP/2 upgrade request)? [Closed]

> Username: DragonOsman  
> Created at: 2020-08-28 20:57:41 UTC  
> Updated at: 2020-08-28 21:06:17 UTC  
> Closed at: 2020-08-28 20:58:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2075  

### Version of Beast  
`#define BOOST_BEAST_VERSION 300`  
  
Here's the GitHub [repo](https://github.com/DragonOsman/currency_converter) for the app.    
  
What I want to do is find the ClientHello and look for the HTTP/2 upgrade request in there.  I read in another issue on this repo (closed now) that we have to try to look for ClientHello in the `boost::asio::ssl::stream::handshake` function, but I'm confused about that because we just call the function like this:  
```cpp  
stream.handshake(ssl::stream_base::server, ec);  
if (ec)  
{  
	std::cerr << "Lines 625 and 626:\n";  
	fail(ec, "handshake");  
}  
```  
where do I do the check here, then?  Or did I misunderstand something?  
  
### All relevant compiler information  
The compiler is MSVC.  I'm using Visual Studio Community 2019, version 16.7.2 (I haven't checked for updates yet so this might be behind one update).    
  
I also found [this](https://github.com/boostorg/beast/blob/5154233350d13a08d70f0a3a46c73bb1093225dd/include/boost/beast/core/detect_ssl.hpp#L96), but first I need to know how to use that `boost::asio::ssl::stream::handshake` function to find the ClientHello message.  If I did misunderstand something about that, please let me know and help me out here.  
  
Thanks in advance.
