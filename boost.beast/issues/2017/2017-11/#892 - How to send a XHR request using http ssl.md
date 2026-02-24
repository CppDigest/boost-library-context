# #892 - How to send a XHR request using http ssl? [Closed]

> Username: lls2wow  
> Created at: 2017-11-17 20:53:44 UTC  
> Updated at: 2017-11-18 17:52:55 UTC  
> Closed at: 2017-11-18 17:52:55 UTC  
> Url: https://github.com/boostorg/beast/issues/892  

Hi @vinniefalco,  
  
     Could you kindly write me a way to perform a XHR request using beast ?  
  
     I can`t find the xhr demo,now I use the http ssl . And I want simulate a xhr request with ssl.

---

## Comment 1

> Username: lls2wow  
> Created at: 2017-11-17 21:13:23 UTC  
> Updated at: 2017-11-17 21:20:32 UTC  
> Url: https://github.com/boostorg/beast/issues/892#issuecomment-345366795  

**The request in chrome:**  
:authority:www.602602e.com  
:method:POST  
:path:/index.php/webcenter/Login/login_do  
:scheme:https  
accept:*/*  
accept-encoding:gzip, deflate, br  
accept-language:zh-CN,zh;q=0.9  
cache-control:no-cache  
content-length:83  
content-type:application/x-www-form-urlencoded; charset=UTF-8  
cookie:type_client=undefined; code=0ab5ffd233e5ca4645228f7fb30549a6_1510948535  
origin:https://www.602602e.com  
pragma:no-cache  
referer:https://www.602602e.com/viewcache/3f381e4642f5ca80c5cce16cfb87e434.html?v=0.0.11  
user-agent:Mozilla/5.0 (iPhone; CPU iPhone OS 8_0_2 like Mac OS X) AppleWebKit/600.1.4 (KHTML, like Gecko) Version/8.0 Mobile/12A366 Safari/600.1.4  
**x-requested-with:XMLHttpRequest**  
  
**The response in chrome:**  
cache-control:no-store, no-cache, must-revalidate, post-check=0, pre-check=0  
content-encoding:gzip  
content-type:text/html; charset=UTF-8  
date:Fri, 17 Nov 2017 19:55:39 GMT  
expires:Thu, 19 Nov 1981 08:52:00 GMT  
pragma:no-cache  
server:nginx/1.12.1  
set-cookie:PHPSESSID=sg9p39b8n6ifc6p2vf0iak01p3; path=/  
set-cookie:uid=13861305; expires=Fri, 17-Nov-2017 20:15:39 GMT; Max-Age=1200; path=/  
status:200  
vary:Accept-Encoding  
x-cache:MISS from hc25  
  
**response`s body were a string "1" if login success.**  
  
I want to simulate this request , but I guess I didn`t use XHR way to send this request ,So I failed.  
  
  
```  
  
void CHttpClient::HttpReq(  
	std::string targetStr,  
	std::map<std::string, std::string> const & params,  
	std::map<std::string, std::string> const & datas,  
	std::map<std::string, std::string> const & headers,  
	OUT std::string& outData)  
{  
	//req  
	http::request<http::string_body> req;  
	req.method(http::verb::post);  
	req.target(targetStr);  
	req.version = 11;  
	req.set(http::field::host, host + ":" +  
		std::to_string(sock.remote_endpoint().port()));  
	//req cookie prepare  
	//req[http::field::cookie] = httpcookie.toString();  
	req.set(http::field::cookie, httpcookie.toString());  
	//std::cout << "cookie string:" << httpcookie.toString() << std::endl;  
	req.set(http::field::user_agent, BEAST_VERSION_STRING);  
	req.set(beast::http::field::content_type, "application/x-www-form-urlencoded");  
	req.body = httpDatasMapToString(datas);  
	//header addtional  
	for (auto it = headers.cbegin(); it != headers.cend(); ++it)  
	{  
		req.set(it->first, it->second);  
	}  
  
  
	req.prepare_payload();  
	std::cout << "request:----------------------------------------------\n" << req << std::endl;  
	http::write(stream, req, ec); if (ec)  fail("write", ec);  
  
	//response  
	beast::flat_buffer b;  
	http::response<http::dynamic_body> res;  
	http::read(stream, b, res, ec); if (ec)  fail("read", ec);//This line failed  
	std::cout << "response:----------------------------------------------\n" << res << std::endl;  
	stringstream ss;  
	ss << beast::buffers(res.body.data());  
	outData = ss.str();  
  
	//parseCookie  
	stringstream sssCookies;  
	sssCookies << res[http::field::set_cookie];  
	ParseCookie(sssCookies.str());  
}  
```  
  
  
**These code failed at this line : http::read(stream, b, res, ec); if (ec)  fail("read", ec);**  
  
**Console print  "read: end of stream"**

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-11-17 21:26:35 UTC  
> Url: https://github.com/boostorg/beast/issues/892#issuecomment-345369674  

Are you performing the SSL handshake? Where is the code to initialize `stream`?

---

## Comment 3

> Username: lls2wow  
> Created at: 2017-11-17 21:32:05 UTC  
> Url: https://github.com/boostorg/beast/issues/892#issuecomment-345370888  

```  
CHttpClient::CHttpClient(ssl::context_base::method ctxMethod, std::string inHost, std::string const cert)  
	: ios(), ec(), r(ios), sock(ios), ctx(ctxMethod), stream(sock, ctx), host(inHost)  
{  
	auto const lookup = r.resolve({ host, "https" }, ec);  
  
	ctx.add_certificate_authority(  
		boost::asio::buffer(cert.data(), cert.size()), ec); if (ec)  fail("add_certificate_authority", ec);  
	// Make the connection on the IP address we get from a lookup  
	boost::asio::connect(sock, lookup, ec); if (ec) fail("connect", ec);  
  
	stream.set_verify_mode(ssl::verify_peer | ssl::verify_fail_if_no_peer_cert);  
	stream.handshake(ssl::stream_base::client, ec); if (ec) fail("handshake", ec);  
}  
```  
  
**I initialize the http in the httpclient constructor with a certificate,**

---

## Comment 4

> Username: lls2wow  
> Created at: 2017-11-17 21:36:44 UTC  
> Url: https://github.com/boostorg/beast/issues/892#issuecomment-345372167  

```  
	http::read(stream, b, res, ec); if (ec)  fail("read", ec);  
	std::cout << "response:----------------------------------------------\n" << res << std::endl;  
```  
  
These two line print on console like this:  
  
read: end of stream  
response:----------------------------------------------  
HTTP/1.1 200 OK  
  
  
Press any key to continue. . .

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-11-17 21:38:54 UTC  
> Url: https://github.com/boostorg/beast/issues/892#issuecomment-345372637  

`end_of_stream` means the remote host closed the connection without sending any data. Probably there is something in your request that it doesn't like. Maybe it didn't like your client certificate. Or maybe it didn't like one of the HTTP headers.

---

## Comment 6

> Username: lls2wow  
> Created at: 2017-11-17 21:44:36 UTC  
> Url: https://github.com/boostorg/beast/issues/892#issuecomment-345373907  

Ok , I`ll try add the normal headers to my code and try again, Thank you very much @vinniefalco

---

## Comment 7

> Username: lls2wow  
> Created at: 2017-11-17 21:45:07 UTC  
> Url: https://github.com/boostorg/beast/issues/892#issuecomment-345374021  

So it doesn`t relate to xhr?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-11-17 21:47:09 UTC  
> Updated at: 2017-11-17 21:47:18 UTC  
> Url: https://github.com/boostorg/beast/issues/892#issuecomment-345374441  

I have no idea what **xhr** is, but if you are sending a correct HTTP request to a server then you should get back at least one correct HTTP response. If you are sending the request but not receiving the response, it means the server decided not to send you the response for some reason. Instead, it looks like it is closing the connection. Therefore the server doesn't like your request, your certificate, your IP address, or the DNS name associated with your IP address.

---

## Comment 9

> Username: lls2wow  
> Created at: 2017-11-17 22:08:56 UTC  
> Updated at: 2017-11-17 22:09:17 UTC  
> Url: https://github.com/boostorg/beast/issues/892#issuecomment-345379163  

Is every request should with handshake? Now my code handshake once ,and first request works ok, second request receive none. (second request occurs problem with this #892)

---

## Comment 10

> Username: lls2wow  
> Created at: 2017-11-17 22:20:42 UTC  
> Url: https://github.com/boostorg/beast/issues/892#issuecomment-345381594  

Hi problem resolved. I should  request once and then close the stream .  next request should handshake again . @vinniefalco  Thank you!

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-11-17 22:34:51 UTC  
> Url: https://github.com/boostorg/beast/issues/892#issuecomment-345384348  

\phew, glad to hear you got it resolved!

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-11-18 17:52:54 UTC  
> Url: https://github.com/boostorg/beast/issues/892#issuecomment-345458812  

It looks like this issue is resolved so I'll go ahead and close it. Feel free to re-open or create a new issue if this problem or another problem comes up, thanks!
