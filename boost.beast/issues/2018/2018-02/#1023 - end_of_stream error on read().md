# #1023 - end_of_stream error on read() [Closed]

> Username: DragonOsman  
> Created at: 2018-02-15 22:12:59 UTC  
> Updated at: 2018-02-22 00:54:29 UTC  
> Closed at: 2018-02-22 00:54:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1023  

Version of Beast is 150.  
  
My code is here: https://gist.github.com/DragonOsman/9a7d4ee116efc575dece7d6de46bd31d .  Program output is:  
```  
POST body example: -----------------------------7e21565302ae  
Content-Disposition: form-data; name="currency_amount"  
  
125.00  
-----------------------------7e21565302ae  
Content-Disposition: form-data; name="to_currency"  
  
PKR  
-----------------------------7e21565302ae--  
  
parsed_value map:  
currency_amount: 125.00  
to_currency: PKR  
Line 477: to_currency is: PKR  
Message in ec is: end of stream  
message body received is:  
Line 671: Error: [json.exception.parse_error.101] parse error at 1: syntax error - unexpected end of input; expected '[', '{', or a literal  
```  
  
My compiler is MSVC (latest version on Visual Studio 2017 IDE).  OS is Windows 10.    
  
I saw on the Beast documentation that an `eof` can cause an `end_of_stream` error.  I also saw in the description of the error itself that it can be caused by either no new octets having been read before the `eof` character was encountered or when the semantics of the message indicate that the connection should be closed to indicate the end of the message.    
  
How do I tell if my problem is because of `eof` or not?  It's possible that the reason for this is related to the error from the JSON library whose message is printed on Line 671, though.  I've asked about that on the JSON library's GitHub page.  And of course, this is not a bug report.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-02-15 22:42:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1023#issuecomment-366086489  

Actually, I think this is a bug in the documentation. `write` and `async_write` used to return the `end_of_stream` error to inform the caller that the connection should be closed, but that is no longer the case (since it interfered with other things). Now, you should just check the return value of  `message::keep_alive()` after sending a message. So `end_of_stream` always means that the remote host closed the connection. I will mark this issue as a documentation bug, thanks!

---

## Comment 2

> Username: DragonOsman  
> Created at: 2018-02-15 22:47:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1023#issuecomment-366087776  

Thanks for the reply and clarification.  
  
Does that also apply to `read` as well, since that's the function returning the error in my code?  
  
So the host closed the connection?  But I added the call `res.keep_alive(true);` on line 654, didn't I?  Why didn't that do anything?  What else do I need to do to fix the error?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-02-15 23:02:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1023#issuecomment-366091454  

I have no idea why the host closed the connection. Keep-Alive is only a suggestion, peers are free to ignore (and often do).

---

## Comment 4

> Username: DragonOsman  
> Created at: 2018-02-21 20:37:48 UTC  
> Updated at: 2018-02-21 20:42:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1023#issuecomment-367463838  

I changed APIs, and now the request seems to be sent successfully, but I could still be making a mistake since for some reason, the request isn't being made correctly (from what I can make of the situation).    
  
The URL I'm trying to make the request to is (I'm substituting the access key dummy text here):  
`http://apilayer.net/api/live?access_key=ACCESS_KEY&currencies=PKR`  
  
The code where I'm making the request is:  
```cpp  
std::string host{ "apilayer.net" }, api_endpoint{ "/api/live" },  
	access_key{ "ACCESS_KEY" }, currencies_param{ query_data },   
	target = api_endpoint + "?access_key=" + access_key + "&currencies=" + currencies_param,  
	port{ "80" };  
int version = 11;  
std::cout << "Debug (Line 631): currencies_param=" << currencies_param << '\n'  
	<< "URL is http://" << host + target << '\n';  
  
// This object and the TCP socket perform our IO  
tcp::resolver resolver{ m_ioc };  
  
// Look up the domain name  
const auto results = resolver.resolve(host, port);  
  
// Make the connection on the IP address we get from a lookup  
boost::asio::connect(m_socket, results.begin(), results.end());  
  
// Set up an HTTP GET request message  
http::request<http::string_body> req{ http::verb::get, target, version };  
req.set(http::field::host, host);  
req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
// Send the HTTP request to the remote host  
http::write(m_socket, req);  
```  
  
Is there something I'm doing wrong here?  If so, what?  Please help me out.  Thanks in advance.  
  
For reference, the complete C++ code is here: https://gist.github.com/DragonOsman/9a7d4ee116efc575dece7d6de46bd31d (of course, with the dummy text for the access key in there in place of the actual thing).
