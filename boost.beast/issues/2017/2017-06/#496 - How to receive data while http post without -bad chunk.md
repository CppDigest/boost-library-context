# #496 - How to receive data while http post without "bad chunk"? [Closed]

> Username: octopus-prime  
> Created at: 2017-06-15 15:22:31 UTC  
> Updated at: 2017-06-15 19:50:37 UTC  
> Closed at: 2017-06-15 17:47:03 UTC  
> Url: https://github.com/boostorg/beast/issues/496  

While porting a client from curl to beast (v57 and v58) i am not able to receive data in rpc (http post).  
  
```  
class client_impl : public virtual client_base  
{  
public:  
    client_impl(const std::string& host, const std::string& port)  
    :  
        _host(host),  
        _service(),  
        _socket(_service)  
    {  
        std::clog << BEAST_VERSION_STRING << std::endl;  
        boost::asio::ip::tcp::resolver resolver{_service};  
        boost::asio::ip::tcp::resolver::query query{host, port};  
        boost::asio::connect(_socket, resolver.resolve(query));  
    }  
  
    virtual value_t call(const string_t& service, const string_t& method, const list_t& arguments) override  
    {  
        // Send HTTP request using beast  
        beast::http::request<beast::http::string_body> request;  
        request.method(beast::http::verb::post);  
        request.target(service);  
        request.insert(beast::http::field::host, _host);  
        request.insert(beast::http::field::user_agent, "libhessian/1.0");  
        request.insert(beast::http::field::accept, "*/*");  
        request.insert(beast::http::field::content_type, "x-application/hessian");  
        request.body = generate(method, arguments);  
        request.prepare();  
        std::clog << request << std::endl;  
        beast::http::write(_socket, request);  
  
        // Receive and print HTTP response using beast  
        beast::flat_buffer buffer;  
        beast::http::response<beast::http::string_body> response;  
        beast::http::read(_socket, buffer, response); // ERROR HERE  
        std::clog << response << std::endl;  
  
        const content_t content = parse(response.body);  
        return boost::apply_visitor(content_visitor(), content);  
    }  
  
    virtual bool negotiate(const string_t& resource) override  
    {  
        throw std::runtime_error("beast http does not support spnego.");  
    }  
  
private:  
    std::string _host;  
    boost::asio::io_service _service;  
    boost::asio::ip::tcp::socket _socket;  
};  
```  
  
The request seems to be ok (same as in curl):  
  
```  
POST /test/test2 HTTP/1.1  
Host: hessian.caucho.com  
User-Agent: libhessian/1.0  
Accept: */*  
Content-Type: x-application/hessian  
Content-Length: 15  
  
HC	replyTrue�  
```  
  
But while reading the response i always get "bad chunk" error.  
  
Verbose info from same call using curl:  
  
```  
*   Trying 54.235.109.78...  
* TCP_NODELAY set  
* Connected to hessian.caucho.com (54.235.109.78) port 80 (#0)  
> POST /test/test2 HTTP/1.1  
Host: hessian.caucho.com  
User-Agent: libhessian/1.0  
Accept: */*  
Content-Type: x-application/hessian  
Content-Length: 15  
  
* upload completely sent off: 15 out of 15 bytes  
< HTTP/1.1 200 OK  
< Server: Resin/4.0.s150303  
< Content-Type: x-application/hessian  
< Transfer-Encoding: chunked  
< Date: Thu, 15 Jun 2017 14:59:50 GMT  
<   
* Curl_http_done: called premature == 0  
* Connection #0 to host hessian.caucho.com left intact  
```  
  
So i missed something in the impl?!  
  
Best regard,  
Mike...

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-15 15:27:23 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308773017  

It looks like a bug, can you show me exactly what the serialized response on the wire looks like? I will write a test and debug it right away!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-15 15:35:09 UTC  
> Updated at: 2017-06-15 15:35:59 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308775329  

I see you have `flat_buffer` as a local variable in your function. Note that this value *has to persist across calls to read*. If you are trying to read more than one HTTP message on  the connection, the lifetime of the buffer must be at least equal to the lifetime of all the messages you want to read. I suggest making the buffer a member of your `client_impl` class.   
  
See the first paragraph under **Reading** here:  
http://vinniefalco.github.io/beast/beast/http/streams.html  
  
_"Because a serialized header is not length-prefixed, algorithms which parse messages from a stream may read past the end of a message for efficiency. To hold this surplus data, all stream read operations use a passed-in DynamicBuffer which must be persisted between calls."_

---

## Comment 3

> Username: octopus-prime  
> Created at: 2017-06-15 16:05:55 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308786752  

Tried flat_buffer and multi_buffer as member of the impl (instead of local variable in method).  
No success. Still "bad chunk".  
  
What do you mean with "serialized response on the wire"? Something like tcpdump files?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-15 16:09:38 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308788811  

>What do you mean with "serialized response on the wire"? Something like tcpdump files?  
  
Yes, the exact bytes which are being sent to Beast, so I can set up a test. But before we do that, is this happening on the first message of the connection?

---

## Comment 5

> Username: octopus-prime  
> Created at: 2017-06-15 16:12:59 UTC  
> Updated at: 2017-06-15 16:13:59 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308790416  

Here are 2 dumps:  
One from call using beast.  
One from call using curl.  
[dumps.zip](https://github.com/vinniefalco/Beast/files/1078255/dumps.zip)  
  
Created with:  
  
`sudo tcpdump -A -i eno1 -w dump tcp and src hessian.caucho.com`

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-06-15 16:17:14 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308791956  

Hmm that does not look like an HTTP response in the dump, I see null bytes. Where's the header?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-06-15 16:18:21 UTC  
> Updated at: 2017-06-15 16:18:36 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308792236  

This is what I'd like to see:  
```  
HTTP/1.1 200 OK\r\n  
Server: test\r\n  
Expect: Expires, MD5-Fingerprint\r\n  
Transfer-Encoding: chunked\r\n  
\r\n  
5\r\n  
*****\r\n  
2;a;b=1;c=\"2\"\r\n  
--\r\n  
0;d;e=3;f=\"4\"\r\n  
Expires: never\r\n  
MD5-Fingerprint: -\r\n  
\r\n  
```

---

## Comment 8

> Username: octopus-prime  
> Created at: 2017-06-15 16:20:26 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308792796  

That's what i see:  
  
```  
<D4>ò<A1>^B^@^D^@^@^@^@^@^@^@^@^@^@^@^D^@^A^@^@^@<AF><AE>BY<87>^N^E^@J^@^@^@J^@^@^@p<8B><CD>M<FD><9F>41<C4><EF><8B>^@E ^@<^@^@@^@*^F<E9><9F>6<EB>mN<C0><A8>^BESC^@P<D6>xX<D0><E5>(<89>   
<D0><FE><A0>^R8<90>^H<C9>^@^@^B^D^E<84>^D^B^H  
<BA><B8><C4>^F+^T<87>^V^A^C^C^F<AF><AE>BY<88><AA>^F^@B^@^@^@B^@^@^@p<8B><CD>M<FD><9F>41<C4><EF><8B>^@E ^@4@^@*^Fȟ6<EB>mN<C0><A8>^BESC^@P<D6>xX<D0><E5>)<89> Ѧ<80>^P^@<F3>n$^@^@^A^A^H  
<BA><B8><C4>!+^T<87>0<AF><AE>BY<9C><AA>^F^@<E1>^@^@^@<E1>^@^@^@p<8B><CD>M<FD><9F>41<C4><EF><8B>^@E ^@<D3>!      @^@*^F<C7><FF>6<EB>mN<C0><A8>^BESC^@P<D6>xX<D0><E5>)<89> Ѧ<80>^X^@<F3>  
<BC><F1>^@^@^A^A^H  
<BA><B8><C4>!+^T<87>0HTTP/1.1 200 OK  
Server: Resin/4.0.s150303  
Content-Type: x-application/hessian  
Transfer-Encoding: chunked  
Date: Thu, 15 Jun 2017 16:26:50 GMT  
  
0005  
H^B^@RT<AF><AE>BY)<AB>^F^@I^@^@^@I^@^@^@p<8B><CD>M<FD><9F>41<C4><EF><8B>^@E ^@;!  
@^@*^FȖ6<EB>mN<C0><A8>^BESC^@P<D6>xX<D0><E5>ȉ Ѧ<80>^X^@<F3>^\R^@^@^A^A^H  
<BA><B8><C4>!+^T<87>0  
0  
  
```  
  
As you can see there are headers:  
  
```  
HTTP/1.1 200 OK  
Server: Resin/4.0.s150303  
Content-Type: x-application/hessian  
Transfer-Encoding: chunked  
Date: Thu, 15 Jun 2017 16:26:50 GMT  
  
```

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-06-15 16:21:24 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308793069  

It looks like there's a 5-byte chunk followed by a final chunk?

---

## Comment 10

> Username: octopus-prime  
> Created at: 2017-06-15 16:21:33 UTC  
> Updated at: 2017-06-15 16:24:48 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308793105  

Note the serialized data (payload, body) is binary! Can contain '\x00'.  
The expected data (5 bytes) are:  
  
'H' '\x02' '\x00' 'R' 'T'  
  
All other bytes... i don't know...

---

## Comment 11

> Username: octopus-prime  
> Created at: 2017-06-15 16:26:21 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308794351  

Do you like some source code to reproduce?

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-06-15 16:28:04 UTC  
> Updated at: 2017-06-15 16:28:53 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308794810  

Okay, `0x0d0a` is the hex code for `"\r\n"` which marks the end of lines in the structured part of the HTTP message. From your curl.dump, I see:  
```  
               0d0a 0d0a 3030 3035 0d0a  
4802 0052 54e7 ae42 59ba fc03 0049 0000  
0049 0000 0070 8bcd 4dfd 9f34 31c4 ef8b  
3608 0045 2000 3bdd 3840 002b 060b 6836  
eb6d 4ec0 a802 1b00 50d6 7ca3 a028 2fcc  
d006 0480 1800 f3a9 ff00 0001 0108 0aba  
b8fa a62b 14bd b50d 0a30 0d0a 0d0a e7ae  
4259 0387 0500 4200 0000 4200 0000 708b  
cd4d fd9f 3431 c4ef 8b36 0800 4520 0034  
dd39 4000 2b06 0b6e 36eb 6d4e c0a8 021b  
0050 d67c a3a0 2836 ccd0 0605 8011 00f3  
faf6 0000 0101 080a bab8 fabf 2b14 bdcf  
```  
  
This looks exactly like the chunk encoded body, we have first `0d0a 0d0a` which means `"\r\n\r\n"` this marks the end of the HTTP header. Then we have `3030 3035 0d0a` which is the string `0005\r\n`. This means there should be 5 octets in the first chunk.  
  
But after that we have  
```  
4802 0052 54e7 ae  
```  
Those last two bytes should be `0d0a` indicating the end of the chunk. It looks very much like Beast is correct, your chunk is invalid.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-06-15 16:31:13 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308795663  

Could hessian be producing bad chunks?  
http://permalink.gmane.org/gmane.text.xml.hessian.interest/688

---

## Comment 14

> Username: octopus-prime  
> Created at: 2017-06-15 16:39:46 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308797823  

Good question. I don't know...  
You give hard signs/evidences for that.  
On the other side... i tried 3 http impls (Poco, cpp-netlib and curl). All 3 working.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2017-06-15 16:48:27 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308800033  

If you are able to use a debugger to get a breakpoint and step through the Beast code, that could help. This function parses the chunk header, its very easy to understand. The error is coming from there:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/impl/basic_parser.ipp#L503

---

## Comment 16

> Username: octopus-prime  
> Created at: 2017-06-15 17:04:21 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308805139  

Small code to reproduce the problem:  
  
```  
#include <beast/core.hpp>  
#include <beast/http.hpp>  
#include <boost/asio.hpp>  
#include <boost/lexical_cast.hpp>  
#include <iostream>  
#include <string>  
  
using namespace std::string_literals;  
  
void test()  
{  
    // Normal boost::asio setup  
    std::string const host = "hessian.caucho.com";  
    boost::asio::io_service ios;  
    boost::asio::ip::tcp::resolver resolver{ios};  
    boost::asio::ip::tcp::socket socket{ios};  
    boost::asio::ip::tcp::resolver::query query{host, "http"};  
    boost::asio::connect(socket, resolver.resolve(query));  
  
    // Send HTTP request using beast  
    beast::http::request<beast::http::string_body> request;  
    request.method(beast::http::verb::post);  
    request.target("/test/test2");  
    request.insert(beast::http::field::host, host + ":" +  
        boost::lexical_cast<std::string>(socket.remote_endpoint().port()));  
    request.insert(beast::http::field::user_agent, "libhessian/1.0");  
    request.insert(beast::http::field::accept, "*/*");  
    request.insert(beast::http::field::content_type, "x-application/hessian");  
    request.body = "H\x02\x00""C\x09""replyTrue\x90"s;  
    request.prepare();  
    std::clog << request << std::endl;  
    beast::http::write(socket, request);  
  
    // Receive and print HTTP response using beast  
    beast::multi_buffer buffer;  
    beast::http::response<beast::http::string_body> response;  
    beast::http::read(socket, buffer, response);  
    std::clog << response << std::endl;  
}  
  
int main()  
{  
	try  
	{  
		test();  
	}  
	catch (std::exception const& exception)  
	{  
		std::cerr << "error: " << exception.what() << std::endl;  
	}  
	return 0;  
}  
```

---

## Comment 17

> Username: vinniefalco  
> Created at: 2017-06-15 17:04:48 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308805305  

Excellent!

---

## Comment 18

> Username: vinniefalco  
> Created at: 2017-06-15 17:23:15 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308811582  

Great job, that example code made it easy to find. I have located the problem, I will have a fix for you shortly!

---

## Comment 19

> Username: octopus-prime  
> Created at: 2017-06-15 17:25:11 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308812085  

That's great news :-)

---

## Comment 20

> Username: vinniefalco  
> Created at: 2017-06-15 17:26:40 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308812471  

**v58** has the fix, its in the pull request queue

---

## Comment 21

> Username: octopus-prime  
> Created at: 2017-06-15 17:35:55 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308814837  

Great job! Works like a charm :-) Thank you!!

---

## Comment 22

> Username: octopus-prime  
> Created at: 2017-06-15 17:47:03 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308817705  

Best of luck for incubation!!

---

## Comment 23

> Username: vinniefalco  
> Created at: 2017-06-15 17:47:14 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308817748  

Always use `flat_buffer` when performing HTTP reads.

---

## Comment 24

> Username: octopus-prime  
> Created at: 2017-06-15 17:47:39 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308817861  

Ok! Thanks!!

---

## Comment 25

> Username: octopus-prime  
> Created at: 2017-06-15 19:49:09 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308847491  

Execution times of example  
  
1) curl http  
```  
real	0m9,201s  
user	0m0,024s  
sys	0m0,008s  
```  
  
2) beast http  
```  
real	0m4,570s  
user	0m0,004s  
sys	0m0,008s  
```

---

## Comment 26

> Username: vinniefalco  
> Created at: 2017-06-15 19:50:37 UTC  
> Url: https://github.com/boostorg/beast/issues/496#issuecomment-308847799  

And I haven't spent any time optimizing yet!!
