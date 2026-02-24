# #2135 - Issues with HTTPS POST call [Closed]

> Username: Skewjo  
> Created at: 2020-12-04 14:12:41 UTC  
> Updated at: 2020-12-05 00:53:42 UTC  
> Closed at: 2020-12-05 00:53:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2135  

Good morning Vinnie,  
  
First - thank you for providing beast. I hate networking(probably because I treat so much of it as a black box), and I love how(mostly) easy your library makes it. I tried my best to look through a [similar issue](https://github.com/boostorg/beast/issues/825) before posting, but it didn't seem to resolve my problem (and I get easily lost reading through networking stuff).  
  
I'm using BOOST_BEAST_VERSION 292 managed by VCPKG in a Windows C++ app that I'm working on called SysLat. I've combined a couple of the examples you've provided and seemingly set up a pretty solid HTTP request in not a lot of time(for me anyways...).  
But I am having an issue when attempting to perform a POST call to an API on my website (https://syslat.com/api/benchmarkData/benchmarkData). I can perform this call to localhost with no issues, the call works directly to the real host when using CURL, but when I run the following I get an error message from the resolver that states ```"resolve: No such host is known```.  
  
```  
inline int upload_data(CSysLatData* dataToSend, char const* host = "https://syslat.com", char const* port = "443", char const* target = "/api/benchmarkData/benchmarkData") {  
    int version = 11;  
    net::io_context ioc;  
    std::make_shared<session>(ioc)->run(dataToSend, host, port, target, version);  
    ioc.run();  
    return EXIT_SUCCESS;  
}  
```  
  
If I change the host to "syslat.com", the resolver no longer errors out, but I get a "308 permanent  redirect" and the upload fails:  
```HTTP/1.1 308 Permanent Redirect  
Date: Fri, 04 Dec 2020 02:48:51 GMT  
Content-Type: text/plain  
Transfer-Encoding: chunked  
Connection: keep-alive  
Location: https://syslat.com/api/benchmarkData/benchmarkData  
Refresh: 0;url=https://syslat.com/api/benchmarkData/benchmarkData  
server: Vercel  
x-vercel-id: cle1::fdbf2-1607050131951-4ba5ae36d506  
  
48  
Redirecting to https://syslat.com/api/benchmarkData/benchmarkData (308)  
  
0  
```  
  
Here's my sample CURL call where "https://syslat.com" seems to work without issue.  
  
Working sample CURL call:   
![image](https://user-images.githubusercontent.com/4118039/101172918-6d382880-3607-11eb-9e39-0d673a8030ac.png)  
  
Are you familiar with this issue?  
Do you think it could be the service hosting my site? (Vercel)  
  
Any help is **greatly** appreciated.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-12-04 14:24:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2135#issuecomment-738811505  

OK, this is pretty simple to solve.  
Firstly it should be said that CURL is a much higher level library than Beast (although I do appreciate that it doesn't feel that way sometimes).  
  
The first problem you have solved yourself: Beast doesn't use URLs - it uses ENDPOINTS. An HTTP URL contains more information than just the endpoint:  
  
e.g.: http://company.com/abc.txt&x=y#marker  
  
This URL has a number of components:  
SCHEME: http  
HOST: company.com  
QUERY: /abc.txt&x=y#marker  
  
The query can be further interpreted:  
path/document: /abc.txt  
parameter x: y  
fragment: "marker"  
  
The QUERY is decoded at the http server and has application-specific meaning.  
The SCHEME and HOST are used on the client to resolve the hostname and set up the TCP and SSL connection.  
  
This is why when you remove the scheme information from the URL, you do actually get a connection.  
  
Next:  
Once you connect, the web server is sending you a response of type 301 REDIRECT.  
  
This is something that CURL is configured by default to detect. CURL's response to this is to _follow_ the redirect using the information sent back in the response headers.  
  
As mentioned, Beast provides low level HTTP functions. You will have to detect the redirect directive yourself and re-request using the data provided in the initial response.

---

## Comment 2

> Username: Skewjo  
> Created at: 2020-12-04 14:36:53 UTC  
> Updated at: 2020-12-04 14:42:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2135#issuecomment-738818337  

Ah, got it. Thank you @madmongo1.   
  
Can you point me to any of the examples that show how to handle a redirect like this?  
  
I'm still a bit confused though, because the redirect message I'm receiving from the server states that the (correct?) Location var is "https://syslat.com/api/benchmarkData/benchmarkData", which is essentially what I'm inputting.   
  
I guess I'm confused at which var of the response I would be using to change and re-issue which argument of the request?  
  
Or maybe I just need to look for a "SCHEME" option in beast so it works the first time without the redirect...

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-12-04 17:10:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2135#issuecomment-738900273  

The SCHEME (with respect to HTTP) simply indicates what the default port should be.  
http = 80  
https = 443  
  
I see you're looking to upload data - presumably you need to set the method of the request to `post` ?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-12-04 17:29:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2135#issuecomment-738912714  

> Can you point me to any of the examples that show how to handle a redirect like this?  
  
To use Beast you are expected to already understand: Asio, HTTP

---

## Comment 5

> Username: Skewjo  
> Created at: 2020-12-04 17:53:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2135#issuecomment-738926555  

@madmongo1, I believe I've set the method correctly to POST with no issues, and I've also set the port to 443 in the first function I posted. The upload to localhost works correctly, and it appears my CURL from above is not receiving any redirect messages(though I may need to turn on verbose output to see that? IDK).  
  
Here's my "run" method that pairs along with the other method above:  
```  
void session::run(CSysLatData* dataToSend, char const* host, char const* port, char const* target, int version)  
{  
    // Set up an HTTP POST request message  
    req_.version(version);  
    req_.method(http::verb::post);  
    req_.target(target);  
    req_.set(http::field::host, host);  
    req_.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
    req_.set(beast::http::field::content_type, "application/json");  
      
    Json::FastWriter fastWriter;  
    std::string output = fastWriter.write(dataToSend->jsonSLD);  
    req_.body() = output;  
  
    std::ostringstream debugOut;  
    debugOut << req_ << std::endl;  
    OutputDebugStringA(debugOut.str().c_str());  
    req_.prepare_payload();  
  
    // Look up the domain name  
    resolver_.async_resolve(  
        host,  
        port,  
        beast::bind_front_handler(  
            &session::on_resolve,  
            shared_from_this()));  
}  
```   
  
@vinniefalco, that's not really fair, is it? I've been as polite as I can possibly be. From my (albeit novice) eyes, it appears your library has different behavior than one of the most commonly used client tools in the industry. Isn't the ease of use at least worth a short discussion?  
  
But my question still stands - if I implement a redirect, what do I feed it? Because the location it gives me (with https:// in front of the name) is the same location that the resolver is failing to match a host for.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-12-04 18:17:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2135#issuecomment-738939627  

> @vinniefalco, that's not really fair, is it?  
  
It is totally fair. What I'm saying, is that the answers to your questions are found in the HTTP RFC (i.e. how to handle redirects). There's nothing wrong with asking here, but note that Beast is not Curl nor will it ever be. It is just a low-level HTTP protocol implementation. Handling redirects, authentication, compression, and other things is the responsibility of the caller.

---

## Comment 7

> Username: Skewjo  
> Created at: 2020-12-04 21:18:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2135#issuecomment-739026315  

Finally had a friend that was nice enough to tell me that I'm an idiot and that HTTPS is basically synonymous with SSL.  
  
That allowed me to find and integrate [this async SSL example](https://github.com/boostorg/beast/blob/develop/example/http/client/async-ssl/http_client_async_ssl.cpp) into my existing code.  
  
Then I was able to plug [this sample](https://www.boost.org/doc/libs/develop/libs/beast/example/common/root_certificates.hpp) in and replace it with my provider's cert, and everything seems to be in much better order now. I'm actually kind of dumbfounded by how quickly it came together. I am now getting a "shutdown: stream truncated" message, but I believe that may be because I've changed my data format.  
  
```  
HTTP/1.1 200 OK  
Content-Type: application/json  
Connection: keep-alive  
x-nextjs-page: /api/benchmarkData/benchmarkData  
date: Fri, 04 Dec 2020 21:07:13 GMT  
content-length: 2  
x-vercel-cache: MISS  
server: Vercel  
x-vercel-id: cle1::sfo1::cxcqs-1607116031684-83c0b2bba165  
strict-transport-security: max-age=63072000  
cache-control: s-maxage=0  
  
{}  
shutdown: stream truncated  
```  
  
Anyways, thanks for the help.
