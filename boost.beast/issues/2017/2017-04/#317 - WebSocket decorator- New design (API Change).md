# #317 - WebSocket decorator: New design (API Change) [Closed]

> Username: redboltz  
> Created at: 2017-04-24 03:41:48 UTC  
> Updated at: 2017-05-08 00:03:59 UTC  
> Closed at: 2017-05-08 00:03:59 UTC  
> Url: https://github.com/boostorg/beast/issues/317  

According to the following documents:  
http://vinniefalco.github.io/beast/beast/ref/websocket__decorate.html  
  
decorator calls operator() of the user defined decorator class that have the following member function (operator).  
  
```cpp  
    template<bool isRequest, class Body, class Fields>  
    void  
    operator()(http::message<isRequest, Body, Fields>& m);  
```  
  
I implemented my decorator. It used to work well. However after I updated Beast (master), it doesn't work. I checked my code. After some analysis, I noticed that the operator() of my class is not called, and not instantiated.  
  
I refer to examples and tests. Then I found the following commit:  
  
https://github.com/vinniefalco/Beast/commit/578dcd0a7857b54b13497c33371e18a4bc91dea7#diff-5c6b99aa6c5ca79e8644be360e76840dR126  
  
It seems that the concept is changed as follows:  
  
```cpp  
    template<bool isRequest, class Fields>  
    void  
    operator()(http::header<isRequest, Fields>& m) const;  
```  
  
Is that right?   
  
If it is right, is there any document about the change?   
  
The issume might relate to #314.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-04-24 17:18:15 UTC  
> Updated at: 2017-04-24 17:18:29 UTC  
> Url: https://github.com/boostorg/beast/issues/317#issuecomment-296753692  

Yes, the requirements changed. Decorators are a sore subject! I don't like the current interface, for one of the reasons that you discovered. The compiler does not warn you or generate an error if you declare your decorator incorrectly.  
  
I'm in the middle of rewriting the decorator interface to be easier to use and also more powerful. It will work like this:  
```  
    using request_filter = std::function<void(request_type&, error_code&)>;  
    using response_filter = std::function<void(response_type&, error_code&)>;  
  
    void set_option(request_filter&& f);  
    void set_option(response_filter&& f);  
```  
  
When operating in both client and server mode, both filters will be called. On the client, the request filter is called after Beast forms the Upgrade request (so callers can "decorate" the request with the User-Agent, subprotocol, or other application specific data). And then the response filter is called after Beast receives the Upgrade response. The `error_code` parameter allows the client to fail the handshake for any reason. For example, to implement Basic Authentication.  
  
On the server side, the request filter is called when Beast receives that Upgrade request. The `error_code` parameter allows the server to fail the request if it wants to. This is a local failure, which won't send back an HTTP response. After Beast builds the HTTP response, it will call the response filter. This gives the server an opportunity to modify the headers. For example, to set the Server field or to respond to subprotocols. The server can change a successful HTTP Upgrade response (101 Switching Protocols) to a failing response, and Beast will detect the status code change and act accordingly. Or the server can do a hard local failure by setting the error code.  
  
In the new interface, it will be a compilation error if you construct a `request_filter` or `response_filter` with a function object whose signature doesn't match. This will fix the problem originally reported in this issue.  
  
I'm open to any feedback, positive or negative, on this new system. Speak up now before it gets merged!  
  
**Note**: I have changed the subject of your issue to reflect the new discussion.

---

## Comment 2

> Username: redboltz  
> Created at: 2017-04-27 00:50:58 UTC  
> Url: https://github.com/boostorg/beast/issues/317#issuecomment-297580894  

Thank you for the comment. I use updated (current) interface.  
  
You mentioned https://github.com/vinniefalco/Beast/issues/212#issuecomment-297562663, is this decorator interface future design also changed?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-04-27 01:23:01 UTC  
> Url: https://github.com/boostorg/beast/issues/317#issuecomment-297585177  

Everything that I have discussed so far, is no longer valid. I have come up with a new design that solves all of the existing problems, is more efficient / takes up less resources, and permits callers complete freedom for dealing with the HTTP Upgrade request and response sequence. Its in the **b36** branch, you can see the work here:  
  
https://github.com/vinniefalco/Beast/commits/b36  
  
This is a summary of those changes (from the commit message):  
  
websocket::stream now provides the following families of  
functions for performing handshakes:  
  
When operating in the server role:  
  
    * stream::accept  
    * stream::accept_ex  
    * stream::async_accept  
    * stream::async_accept_ex  
  
When operating in the client role:  
  
    * stream::handshake  
    * stream::handshake_ex  
    * stream::async_handshake  
    * stream::async_handshake_ex  
  
Member functions ending with "_ex" allow an additional  
RequestDecorator parameter (for the accept family of  
functions) or ResponseDecorator parameter (for the  
handshake family of functions).  
  
The decorator is called to optionally modify the contents  
of the HTTP request or HTTP response object generated by  
the implementation, before the message is sent. This  
permits callers to set the User-Agent or Server fields,  
add or modify HTTP fields related to subprotocols, or  
perform any required transformation of the HTTP message  
for application-specific needs.  
  
handshake() and handshake_ex() now return the  
HTTP Response object received from the remote endpoint,  
allowing callers to take additional action based on the  
contents of the HTTP header fields. For example, to  
verify that the server returned the correct Basic  
Authentication credentials.  
  
The completion handlers passed to the functions  
async_handshake() and async_handshake_ex() now take an  
additional parameter `response_type`, which provides the  
same information as the return value of the synchronous  
versions of those functions above.  
  
The new implementation does not require any state to be  
stored in the stream object. Therefore, websocket::stream  
objects are now smaller in size.  
  
The overload of set_option for setting a decorator on the  
stream is removed. The only way to set decorators now is  
with a suitable overload of accept or handshake.
