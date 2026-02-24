# #1222 - Move or swap request headers? [Closed]

> Username: carolinebeltran  
> Created at: 2018-08-10 22:45:28 UTC  
> Updated at: 2018-09-04 14:02:30 UTC  
> Closed at: 2018-09-04 14:02:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1222  

If you have two requests, is it possible to swap the fields container, or assign **headerReq**'s container to **req**?  
  
```  
http::request<http::string_body> req;  
http::request_parser<http::empty_body> headerReq;  
```  
  
I've been very comfortable using **http::request** but if the above is not possible, it looks to me that I will instead have to use **http::request_parser** as documented in the link below:  
  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/more_examples/change_body_type.html

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-08-10 23:10:16 UTC  
> Updated at: 2018-08-10 23:11:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1222#issuecomment-412228887  

You can try `std::swap(req.base(), headerReq.base())`  
  
Or `req.base().swap(headerReq.base())`

---

## Comment 2

> Username: carolinebeltran  
> Created at: 2018-08-11 02:31:02 UTC  
> Updated at: 2018-08-11 02:31:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1222#issuecomment-412245185  

Vinnie, thank you for responding.  I tried:  
  
`std::swap(req.body().data(), headerReq.get().body().data());`  
  
But the pointers are const.  I don't think that it's possible as I had hoped so I'll change my codebase from using request to request_parser and see how that works for me.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-08-11 02:44:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1222#issuecomment-412245791  

It really does sound like you are trying to "change body type" after reading the header, so why not just stick with that? What is your objection to `request_parser`? Serious programs won't be able to use the message-oriented stream algorithms anyway, for many reasons.

---

## Comment 4

> Username: carolinebeltran  
> Created at: 2018-08-12 14:46:36 UTC  
> Updated at: 2018-08-12 14:50:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1222#issuecomment-412347625  

Just noticed a newer version but still using Beast 1.67.  My strictly *async* approach that works wonderfully using *request* does not work with *request_parser*.  
  
With my current design, either *request* or *request_parser* are instance variables and I am limited to not being able to 'empty out' my request each time before I initiate a read, e.g.:  
  
req_ = {};  // OK!  empty out *request*  
reqParser_ = {};	  // compiler ERROR: attempting to reference a deleted function  
  
At the bottom of the following snippet, please notice that reqParser_ is an ivar belonging to my ClientContext class:  
```  
void Listener::onAccept(boost::system::error_code ec) {  
  ...  
  // Create the ClientContext and run it  
  std::make_shared<ClientContext>(std::move(socket_), docRoot_, appServer_)->run();  
  // Accept another connection  
  waitForConnection();  
}  
  
// Handles an HTTP server connection  
class ClientContext : public std::enable_shared_from_this<ClientContext>  
{  
  ...  
private:  
	http::request_parser<http::string_body> reqParser_;  
...  
  
```  
  
I think that my next step will be to declare reqParser_ as a pointer instead, and *explicitly create an instance* (when needed) and *explicitly destroy* it (when done).  
  
P.S.  Unfortunately, I will not be able to try this pointer idea for several days because I am badly swamped at work but am eager to hear your comments if any.  Thank you.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-08-12 18:54:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1222#issuecomment-412363992  

> my next step will be to declare reqParser_ as a pointer instead  
  
Consider instead, storing the parser in `std::optional` (or `boost::optional`) and calling `optional::emplace` every time you want a fresh instance.

---

## Comment 6

> Username: carolinebeltran  
> Created at: 2018-08-12 18:55:35 UTC  
> Updated at: 2018-08-12 19:04:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1222#issuecomment-412364076  

My web browser client gets the expected result from the beast server, e.g. an html or jpg file, but after changing **request** to **request_parser** and writing my own **onReadHeader** method that inspects the header the beast server (running on my Windows 10 development machine) is reporting:  
  
`write: An established connection was aborted by the software in your host machine`  
  
I know that it's me doing something wrong because I am logging what goes on with each request and this is what I am seeing:  
  
```  
ClientContext::run called and will now execute ClientContext::initiateReadHeader  
ClientContext::initiateReadHeader called and will now execute async_read_header  
ClientContext::onReadHeader called and will now execute ClientContext::initiateRead  
ClientContext::initiateRead called and will now execute async_read  
ClientContext::onRead called and will now execute the handleRequest function  
The handleRequest function is called  
ClientContext::initiateReadHeader called and will now execute async_read_header  
ClientContext::onReadHeader called and will now execute ClientContext::initiateRead  
ClientContext::initiateRead called and will now execute async_read  
ClientContext::onRead called and will now execute the handleRequest function  
The handleRequest function is called  
ClientContext::initiateReadHeader called and will now execute async_read_header  
ClientContext::onReadHeader called and will now execute ClientContext::initiateRead  
ClientContext::initiateRead called and will now execute async_read  
ClientContext::onRead called and will now execute the handleRequest function  
The handleRequest function is called  
ClientContext::initiateReadHeader called and will now execute async_read_header  
ClientContext::onReadHeader called and will now execute ClientContext::initiateRead  
ClientContext::initiateRead called and will now execute async_read  
ClientContext::onRead called and will now execute the handleRequest function  
The handleRequest function is called  
write: An established connection was aborted by the software in your host machine  
```  
  
What I'm having trouble figuring out is why my log files are repeating before terminating with the error above.  The log file should normally stop at the first line indicating "**The handleRequest function is called**".  
  
CLEARIFICATION  
  
I want to be clear that using **request** or **request_parser** in either case the web client gets the expected output (jpg or html static or dynamic content).  It's the looping and the error that's bothering me since introducing **request_parser** into my app.

---

## Comment 7

> Username: carolinebeltran  
> Created at: 2018-08-12 18:58:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1222#issuecomment-412364256  

Thank you Vinnie, I will try your "std::optional" suggestion over the next few days.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-08-12 19:28:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1222#issuecomment-412366051  

Okay one thing at a time. Did the `optional` work for you? There is insufficient information in your last message for me to diagnose the problem. Is this an SSL connection? Are you closing the socket and then writing?
