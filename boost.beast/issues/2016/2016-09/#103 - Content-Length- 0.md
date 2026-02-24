# #103 - Content-Length: 0 [Closed]

> Username: e-fominov  
> Created at: 2016-09-30 09:46:09 UTC  
> Updated at: 2016-10-13 13:31:43 UTC  
> Closed at: 2016-10-13 13:31:43 UTC  
> Url: https://github.com/boostorg/beast/issues/103  

If I use `response_v1<string_body>` and if string is empty or if `response_v1<empty_body>` - "Content-Length: 0" header will be automatically sent. It looks incorrect for me  
  
[Here](https://github.com/vinniefalco/Beast/blob/master/include/beast/http/impl/message_v1.ipp#L140) I see:  
  
```  
    if(pi.connection_value != connection::upgrade)  
    {  
        if(pi.content_length)  
        {  
            // VFALCO TODO Use a static string here  
            msg.headers.insert("Content-Length",  
                std::to_string(*pi.content_length));  
        }  
        else if(msg.version >= 11)  
        {  
            msg.headers.insert("Transfer-Encoding", "chunked");  
        }  
    }  
```  
  
But pi.content_length is `boost::optional<std::uint64_t> content_length;`, so I suggest this change:  
  
`if(pi.content_length.is_initialized() && *pi.content_length != 0)`  
  
as all `body_type::writer` classes are returning `uint64_t` for `content_length()` method and not `boost<optional>` - this will lead to `pi.conent_length`  always be initialized but sometimes with zero  
  
This will affect automatic keep-alive in [line 166](https://github.com/vinniefalco/Beast/blob/master/include/beast/http/impl/message_v1.ipp#L166)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-09-30 12:04:03 UTC  
> Updated at: 2016-09-30 12:04:57 UTC  
> Url: https://github.com/boostorg/beast/issues/103#issuecomment-250727822  

This code is working as designed, if there is no body we want Content-Length: 0. If a writer excludes the `content_length` member function then no content length is inserted (optional is disengaged). If the writer provides the `content_length` member function then the content length is unconditionally inserted with the corresponding value, even if zero. Or no? Is this wrong?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-09-30 12:07:01 UTC  
> Url: https://github.com/boostorg/beast/issues/103#issuecomment-250728362  

rfc7230 Section 3.3.2:  
   A user agent SHOULD send a Content-Length in a request message when  
   no Transfer-Encoding is sent and the request method defines a meaning  
   for an enclosed payload body.  For example, a Content-Length header  
   field is normally sent in a POST request even when the value is 0  
   (indicating an empty payload body).  A user agent SHOULD NOT send a  
   Content-Length header field when the request message does not contain  
   a payload body and the method semantics do not anticipate such a  
   body.  
  
Your proposed change would leave out Content-Length: 0 on a POST. I guess if the verb is GET and content length is zero then we should leave it out. Is there a list of verbs and whether or not they anticipate a message body?

---

## Comment 3

> Username: viccpp  
> Created at: 2016-09-30 12:18:57 UTC  
> Updated at: 2016-09-30 12:25:12 UTC  
> Url: https://github.com/boostorg/beast/issues/103#issuecomment-250730408  

https://www.w3.org/Protocols/rfc2616/rfc2616-sec4.html#sec4.3  
  
>  A message-body MUST NOT be included in a request if the specification of the request method (section 5.1.1) does not allow sending an entity-body in requests. A server SHOULD read and forward a message-body on any request; if the request method does not include defined semantics for an entity-body, then the message-body SHOULD be ignored when handling the request.  
  
https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html  
  
> A TRACE request MUST NOT include an entity.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2016-09-30 12:37:42 UTC  
> Updated at: 2016-09-30 12:41:39 UTC  
> Url: https://github.com/boostorg/beast/issues/103#issuecomment-250733808  

How about this:  
  
```  
#include <beast/core/detail/ci_char_traits.hpp>  
...  
    if(pi.connection_value != connection::upgrade)  
    {  
        if(pi.content_length)  
        {  
            struct set_field  
            {  
                void  
                operator()(request_v1<Body, Headers>& msg,  
                    detail::prepare_info const& pi) const  
                {  
                    using beast::detail::ci_equal;  
                    if(*pi.content_length > 0 ||  
                        ci_equal(msg.method, "POST"))  
                    {  
                        msg.headers.insert("Content-Length",  
                            std::to_string(*pi.content_length));  
                    }  
                }  
  
                void  
                operator()(response_v1<Body, Headers>& msg,  
                    detail::prepare_info const& pi) const  
                {  
                    msg.headers.insert("Content-Length",  
                        std::to_string(*pi.content_length));  
                }  
            };  
            set_field{}(msg, pi);  
        }  
        else if(msg.version >= 11)  
        {  
            msg.headers.insert("Transfer-Encoding", "chunked");  
        }  
    }  
```

---

## Comment 5

> Username: vinniefalco  
> Created at: 2016-09-30 12:45:36 UTC  
> Url: https://github.com/boostorg/beast/issues/103#issuecomment-250735305  

You can try it out here:  
https://github.com/vinniefalco/Beast/commits/b15

---

## Comment 6

> Username: e-fominov  
> Created at: 2016-09-30 14:10:30 UTC  
> Url: https://github.com/boostorg/beast/issues/103#issuecomment-250754111  

Is it possible that [this line](https://github.com/vinniefalco/Beast/blob/5ecf8de7d3d20a783dcc88ec577f1d88539e4cd0/include/beast/http/impl/message_v1.ipp#L141) condition will be false? `if (pi.content_length)`  
  
And every server response will have "Content-Length: 0" header.  [RFC 7320](https://tools.ietf.org/html/rfc7230#section-3.3.2) defines several situations when server MUST NOT send this header, for example:  
  
> A server MUST NOT send a Content-Length header field in any response  
>    with a status code of 1xx (Informational) or 204 (No Content).  A  
>    server MUST NOT send a Content-Length header field in any 2xx  
>    (Successful) response to a CONNECT request (Section 4.3.6 of  
>    [RFC7231]).  
  
On server side, if I use `response_v1<empty_body>` - i will have this header in response for any status code . May be empty_body should not have `empty_body::content_length` member, as it returns 0 and `if (pi.content_length)` becomes true.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2016-09-30 14:43:40 UTC  
> Updated at: 2016-09-30 14:44:55 UTC  
> Url: https://github.com/boostorg/beast/issues/103#issuecomment-250762759  

Thank you. That's the information that I need - we need to go through the entire RFC and make sure that all the requirements are respected. Unfortunately, `prepare` doesn't have the original request so we can't suppress it on a CONNECT request. I'll open an issue for that.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2016-09-30 14:46:58 UTC  
> Url: https://github.com/boostorg/beast/issues/103#issuecomment-250763620  

Okay, I made the changes, this is the modified code:  
  
```  
                void  
                operator()(message_v1<false, Body, Headers>& msg,  
                    detail::prepare_info const& pi) const  
                {  
                    if((msg.status / 100 ) != 1 &&  
                        msg.status != 204)  
                    {  
                        msg.headers.insert("Content-Length",  
                            std::to_string(*pi.content_length));  
                    }  
                }  
```  
  
I rewrote the branch in this pull request:  
https://github.com/vinniefalco/Beast/commits/b15

---

## Comment 9

> Username: e-fominov  
> Created at: 2016-09-30 15:20:21 UTC  
> Url: https://github.com/boostorg/beast/issues/103#issuecomment-250772573  

Ok, this will make messages more standard-compiant. There are still many situations that are not covered by this solution.   
Looks like `prepare` function becomes more and more complicated and it makes high-level protocol handling, that should be made somewhere else.   
Do you have plans for making high-level protocol algorithms?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2016-09-30 15:25:56 UTC  
> Url: https://github.com/boostorg/beast/issues/103#issuecomment-250774166  

I don't have any plans to add high-level protocol algorithms to Beast. The intent of this library is to offer only the low level HTTP functionality. I hope that other people (maybe you?) will build on Beast to provide high level functions.  
  
That said, there is no requirement that you call `prepare`. You can just skip the call and set fields manually.

---

## Comment 11

> Username: viccpp  
> Created at: 2016-09-30 17:49:47 UTC  
> Updated at: 2016-09-30 17:50:53 UTC  
> Url: https://github.com/boostorg/beast/issues/103#issuecomment-250808814  

What about status 304 (Not Modified)?  
https://tools.ietf.org/html/rfc7230#section-3.3

---

## Comment 12

> Username: vinniefalco  
> Created at: 2016-09-30 18:21:33 UTC  
> Url: https://github.com/boostorg/beast/issues/103#issuecomment-250816413  

@2underscores-vic Yep, I missed that one, sorry about it! I have updated the b15 branch (force-pushed). Thanks!
