# #348 - update broke code, what's the right change to make? [Closed]

> Username: bitbugprime  
> Created at: 2017-05-09 16:38:42 UTC  
> Updated at: 2017-06-12 08:20:37 UTC  
> Closed at: 2017-06-08 05:35:16 UTC  
> Url: https://github.com/boostorg/beast/issues/348  

1) empty_body disappeared. Is an "uninitialized" string_body the same thing? I was using empty_body to send error responses to the client.  
  
2) beast::http::error::bad_uri is gone. Was that just redundant with bad_method, bad_version, and bad_field?  
  
2) beast::http::parse_error::short_read and beast::http::parse_error::connection_closed also disappeared. boost::asio::error::eof wasn't sufficient by itself to detect when it's time to quit without a response so I added those to my "quit" condition in the catch block for beast::http::async_read.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-05-09 20:38:01 UTC  
> Url: https://github.com/boostorg/beast/issues/348#issuecomment-300293989  

1. You can use `string_body` or you can just send a `header` instead of a `message`, there are `write` overloads for it.  
  
2. You can't get a bad URI, it will show up as a bad version. Beast doesn't do "real" URI parsing. Some day it will, and I will bring that error code back.  
  
3. `http::parse_error::short_read` is now `http::error::partial_message`, and `http::error::end_of_stream` is used to indicate that the stream reached the end of file without getting any message bytes.

---

## Comment 2

> Username: bitbugprime  
> Created at: 2017-05-09 21:16:13 UTC  
> Url: https://github.com/boostorg/beast/issues/348#issuecomment-300304003  

I don't think I understand the header vs message bit. Currently, I send an error response this way:  
  
```  
{  
    ...  
    beast::streambuf sb;  
    beast::http::async_read(sock, sb, request, yc); // yay coroutines  
} catch (boost::system::system_error const& e) {  
    auto code = e.code();  
    if (code == beast::http::error::method) {  
        beast::http::response<beast::http::string_body> response;  
        response.status = 400;  
        response.reason = beast::http::reason_string(400);  
        response.version = 11;  
        beast::http::prepare(response, beast::http::connection::close);  
        beast::http::async_write(sock, response, yc); // I like coroutines  
    }  
}  
```  
  
What error code now indicates connection closed? Is that subsumed in end_of_stream?  
  
Do I still need to check for both beast::http::error::end_of_stream and boost::asio::error;:eof?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-05-09 21:28:22 UTC  
> Updated at: 2017-05-09 21:54:14 UTC  
> Url: https://github.com/boostorg/beast/issues/348#issuecomment-300306932  

Your code using `string_body` is fine, or you can do this:  
```  
    beast::http::response_header response;  
    ...  
```  
  
I'm still trying to resolve some design issues with the HTTP parser and incremental reading, so this could change again before the formal review (scheduled July 1st).  
  
>What error code now indicates connection closed? Is that subsumed in end_of_stream?  
  
* If a connection is closed during an HTTP read and at least one byte has been read, but the message is not complete, the error returned will be `http::error::partial_message`  
  
* If a connection is closed during an HTTP read and no bytes corresponding to the current message have been read, the error returned will be `boost::asio::error::eof`.  
  
> Do I still need to check for both beast::http::error::end_of_stream and boost::asio::error;:eof?  
  
You only need to check for `boost::asio::error::eof`. The other error code is not currently used.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-05-09 21:54:29 UTC  
> Url: https://github.com/boostorg/beast/issues/348#issuecomment-300313115  

I updated my answer on new information

---

## Comment 5

> Username: bitbugprime  
> Created at: 2017-05-09 22:09:38 UTC  
> Url: https://github.com/boostorg/beast/issues/348#issuecomment-300316309  

So you're no longer planning to update the code to return end_of_stream (lack of which you previously said was a bug)?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-05-09 22:10:29 UTC  
> Url: https://github.com/boostorg/beast/issues/348#issuecomment-300316458  

>So you're no longer planning to update the code to return end_of_stream (lack of which you previously said was a bug)?  
  
No, I just misread my own code. `end_of_stream` doesn't seem to be used. I should remove it.

---

## Comment 7

> Username: bitbugprime  
> Created at: 2017-05-09 22:43:39 UTC  
> Url: https://github.com/boostorg/beast/issues/348#issuecomment-300322837  

Seems like a wart on the api to throw beast::http::error::* codes for everything except eof, where you return a boost::asio::error code. If the Networking TS ever gets off the ground you'd have to change it again from boost::asio::error::eof to whatever their error codes are. Why not have end_of_stream to keep the api consistent and a little more independent of the underlying networking library?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-05-09 23:49:12 UTC  
> Url: https://github.com/boostorg/beast/issues/348#issuecomment-300333421  

I could make that change, sure. But lets not kid ourselves, a port of Beast to Networking-TS is going to change quite a lot, the error code is the least of our worries! I will leave this open and consider changing it pending feedback. Thanks!

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-06-08 05:35:16 UTC  
> Url: https://github.com/boostorg/beast/issues/348#issuecomment-307003369  

The error code is now `beast::http::error::end_of_stream`  
  
If you are still having an issue please feel free to re-open this or to create a new issue - thanks!

---

## Comment 10

> Username: mika-fischer  
> Created at: 2017-06-12 08:20:37 UTC  
> Url: https://github.com/boostorg/beast/issues/348#issuecomment-307721465  

@radian Unrelated to this bug, but please note that you cannot use coroutines safely in a catch block. See https://svn.boost.org/trac/boost/ticket/11991
