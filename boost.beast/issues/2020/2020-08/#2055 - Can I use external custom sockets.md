# #2055 - Can I use external custom sockets? [Closed]

> Username: lattice0  
> Created at: 2020-08-18 08:28:05 UTC  
> Updated at: 2020-10-04 03:01:45 UTC  
> Closed at: 2020-10-04 03:01:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2055  

Hello, I'd like to know a simple information. Can I use custom sockets with this library? I'm looking for a library which let me pass my own socket implementation (my own object which implements open, connect, send, etc). I dont have time to read each http library one by one to determine this, so could you kindly tell me if this is possible. If not, then is it easy to add such funcitonality on this library?  
  
thank you so much!

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-08-18 08:50:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2055#issuecomment-675350354  

Hi @lucaszanella , this is more an Asio question. However, the answer is yes.  
  
Are you using the async or sync version?  
  
There are a couple of concepts in Asio which I expect you will need to model:  
  
eg:  
  
Synchronous Interface:  
  
* SyncReadStream https://www.boost.org/doc/libs/1_74_0/doc/html/boost_asio/reference/SyncReadStream.html  
* SyncWriteStream https://www.boost.org/doc/libs/1_74_0/doc/html/boost_asio/reference/SyncWriteStream.html  
  
Asyncronous Interface:  
  
* AsyncReadStream https://www.boost.org/doc/libs/1_74_0/doc/html/boost_asio/reference/AsyncReadStream.html  
* AsyncWriteStream https://www.boost.org/doc/libs/1_74_0/doc/html/boost_asio/reference/AsyncWriteStream.html

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-08-18 11:12:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2055#issuecomment-675416794  

For WebSocket the answer is no. But for HTTP you can also use the buffer-oriented APIs (e.g. `parser` and `serializer`) and do the reading and writing yourself.

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-08-20 13:31:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2055#issuecomment-677666171  

Hi @lucaszanella, do you feel the issue is sufficiently answered for me to close it, or is there something more you would like to ask?

---

## Comment 4

> Username: lattice0  
> Created at: 2020-08-21 05:07:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2055#issuecomment-678040962  

Hi @madmongo1, do you mean implementing my own `AsyncWriteStream, AsyncReadStream` classes? It's kinda hard to get it right (I already did it before for other project).  
  
Looking at https://github.com/boostorg/beast/blob/develop/example/http/client/async/http_client_async.cpp#L26 I see that I have to implement one of those:  
  
```  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
```  
  
maybe the tcp one?  
  
Or maybe I need to tweak https://github.com/boostorg/beast/blob/develop/include/boost/beast/http/read.hpp by passing my own templates for   
  
```  
class SyncReadStream,  
    class DynamicBuffer  
```  
  
and maybe for write.hpp also?   
  
How would I make beast use my classes then?   
  
Looks like it's possible but requires reimplementing `AsyncReadStream` and `AsyncWriteStream` which requires a lot of work. Am I right?   
  
Maybe the sync version is easier, but it follows the same principle I guess

---

## Comment 5

> Username: lattice0  
> Created at: 2020-08-21 05:11:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2055#issuecomment-678041798  

@vinniefalco is there an example on how to force beast to use a custom parser? I'm not very familiar with hpp only libraries, I don't see a way to do `using parser = MyCustomParser` etc...

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-08-21 05:24:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2055#issuecomment-678045339  

Did you have a look here? https://www.boost.org/doc/libs/1_74_0/libs/beast/doc/html/beast/using_http/custom_parsers.html

---

## Comment 7

> Username: stale[bot]  
> Created at: 2020-09-20 05:28:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2055#issuecomment-695747560  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 8

> Username: stale[bot]  
> Created at: 2020-10-04 03:01:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2055#issuecomment-703193610  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
