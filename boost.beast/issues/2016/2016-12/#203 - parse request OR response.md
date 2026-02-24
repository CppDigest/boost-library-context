# #203 - parse request OR response [Closed]

> Username: dirkvdb  
> Created at: 2016-12-01 18:58:47 UTC  
> Updated at: 2017-09-01 13:21:25 UTC  
> Closed at: 2017-09-01 03:05:43 UTC  
> Url: https://github.com/boostorg/beast/issues/203  

I'm implementing an ssdp server. This means I am receiving both broadcasted http requests or responses on a udp socket. There is no guaranteed ordering. So when I receive data it can either be a  request or a response.  
  
Is it possible to use beast in this fashion? The read calls seem to require that you specify if you are reading a request or a response.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-12-01 19:00:09 UTC  
> Url: https://github.com/boostorg/beast/issues/203#issuecomment-264261446  

Currently there is no way to do that, but it is something I can add in the next big update to the parser.

---

## Comment 2

> Username: dirkvdb  
> Created at: 2016-12-01 19:04:43 UTC  
> Url: https://github.com/boostorg/beast/issues/203#issuecomment-264262731  

That would be nice! Can you give an expectation of the timeframe of the next big update?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-12-01 19:06:36 UTC  
> Url: https://github.com/boostorg/beast/issues/203#issuecomment-264263217  

6 weeks give or take. For now, you can try something like this:  
* Read at least 5 bytes into a `streambuf`  
* Check if those bytes match the characters `HTTP/`  
* If yes, then its a response, else its a request  
Now call `beast::http::read` with the appropriate message type, with the same `streambuf`. Beast will use the bytes already in the streambuf before going to the network to get more.

---

## Comment 4

> Username: dirkvdb  
> Created at: 2016-12-01 19:08:47 UTC  
> Url: https://github.com/boostorg/beast/issues/203#issuecomment-264263822  

I already have code that uses the node parser, but I'm planning to migrate to beast. So I'll just wait a bit for now. Thanks for the fast response!

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-02-19 14:53:55 UTC  
> Updated at: 2017-02-19 15:01:03 UTC  
> Url: https://github.com/boostorg/beast/issues/203#issuecomment-280924080  

Just to let you know, that I have not forgotten about this issue. I plan on addressing it as soon as I have finished the refactoring of the HTTP parser and algorithms. Beast will provide this function:  
```  
/** Return `true` if the input sequence might indicate an HTTP request.  
  
    This function will return `true` if the first five bytes in the input  
    sequence do not match the required syntax for an HTTP response,  
    otherwise `false` is returned. If fewer than five bytes are present  
    in the input sequence, the return value will be tribool indeterminate.  
  
    A return value of `true` does not necessarily indicate that the  
    input is an HTTP request, only that it does not match the requirements  
    for an HTTP response. Similarly, a return value of `false` does not  
    imply that input bytes appended to the input sequence will form  
    a valid HTTP response.  
*/  
boost::tribool  
maybe_request(ConstBufferSequence const& buffers);  
```  
  
Base on the return value you could choose the appropriate HTTP message type and algorithm to perform your operation.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-07-04 02:00:43 UTC  
> Url: https://github.com/boostorg/beast/issues/203#issuecomment-312762561  

This should become an example similar to the `detect_ssl` and `async_detect_ssl` example.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-09-01 03:05:43 UTC  
> Url: https://github.com/boostorg/beast/issues/203#issuecomment-326475870  

There is no real use-case for this in an example program so I think we should leave this to another library.

---

## Comment 8

> Username: dirkvdb  
> Created at: 2017-09-01 12:24:52 UTC  
> Updated at: 2017-09-01 12:25:00 UTC  
> Url: https://github.com/boostorg/beast/issues/203#issuecomment-326566970  

> There is no real use-case for this in an example program  
  
What exactly do you mean with this?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-09-01 12:59:35 UTC  
> Url: https://github.com/boostorg/beast/issues/203#issuecomment-326573690  

>What exactly do you mean with this?  
  
I mean that any example program which would use this feature would be completely contrived.  
  
I can help you write the code if you still need this feature though, would you be interested in that? One thing I should point out though, Beast HTTP stream operations don't work on UDP sockets. So I'm not sure how you plan to get around that.

---

## Comment 10

> Username: dirkvdb  
> Created at: 2017-09-01 13:11:59 UTC  
> Url: https://github.com/boostorg/beast/issues/203#issuecomment-326576277  

I'm using regular asio for receiving the messages, but it would be nice if I could use beast for decoding the http messages. Currently I'm using both beast and also have a copy of the nodejs http parser in my project.  
I would like to get rid of the nodejs parser, so it would be nice if I could use the beast parser, but is seems to require the isRequest template argument which I do not know.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-09-01 13:20:53 UTC  
> Updated at: 2017-09-01 13:21:25 UTC  
> Url: https://github.com/boostorg/beast/issues/203#issuecomment-326578276  

I explained in an earlier reply how to do this. Check the first 5 characters of the input buffer and see if they match `"HTTP/"`. If yes, then you have a response, pass `isRequest=false`. Otherwise, pass `isRequest=true`. Example:  
```  
template<class ConstBufferSequence>  
boost::tribool  
is_request(ConstBufferSequence const& buffers)  
{  
    if(boost::asio::buffer_size(buffers) < 5)  
        return boost::indeterminate;  
    char buf[5];  
    boost::asio::buffer_copy(boost::asio::buffer(buf), buffers);  
    return std::memcmp(buf, "HTTP/", 5) != 0;  
}  
```  
  
Is this not sufficient?
