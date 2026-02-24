# #17 - Changes in b4? [Closed]

> Username: vinniefalco  
> Created at: 2016-05-25 16:53:32 UTC  
> Updated at: 2016-06-12 20:50:04 UTC  
> Closed at: 2016-06-12 20:03:41 UTC  
> Url: https://github.com/boostorg/beast/issues/17  

From @randian   
I just checked in and saw many things have changed. For example, methods in request objects are manually specified strings and not enums now? What is the _v1 stuff? What does prepare do? How about a before/after code example to explain these changes?  
  
Response:  
  
Thank you for your interest in Beast!  
  
The method in the request is now a string instead of an enum. Previously, it was an enumeration because the parser being used (https://github.com/nodejs/http-parser) only supported a fixed set of methods. I have since written a new HTTP parser which is header-only (Beast is now completely header-only) and supports any string for the method.  
  
In the previous version, `http::write` and `http::async_write` made a copy of the message and inserted these fields for you. This design caused a number of problems. Now, the `prepare` function will set certain fields in the `message` for you (especially Content-Length):  
http://vinniefalco.github.io/beast/beast/ref/http__prepare.html  
Use of `prepare` is optional, if you want to set Content-Length yourself you may do so. For example, when sending the response to a HEAD request.  
  
I've been collaborating with @vinipsmaker (author of the Boost.Http proposal) and in the interest of making Beast "HTTP/2 ready", the message model has been adjusted. The base class `message` represents any HTTP message including HTTP/2, while the derived class `message_v1` contains the parts of the message that are specific to HTTP/1.0 and HTTP/1.1. For example, `message_v1` has a version, while `message` does not. The `_v1` suffix indicates that the interface is for HTTP/1.X only.  
  
The only explanation of the changes is in the commit messages. This library is currently in the Boost incubator awaiting feedback for subsequent iteration. And Beast is not yet at version 1.0.0. From what I can tell there are not that many users. All interfaces should be considered subject to change, although from a practical perspective I do not anticipate any more significant changes unless Boost reviewers present a strong case for it (with the exception of `http::read` and `http::async_read`, which have some problems in their current interface). However in the future I will try to document API breaking changes.  
  
Once again thank you for your valuable feeback!

---

## Comment 1

> Username: bitbugprime  
> Created at: 2016-05-25 22:35:16 UTC  
> Url: https://github.com/boostorg/beast/issues/17#issuecomment-221728210  

Great. That collaboration is great news. One conspicuous omission in beast is the lack of url/uri handling. You can send and receive using beast but can't process non-trivial GET requests. I'm using Microsoft cpprest to process URIs right now, any plans in this direction?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-05-25 23:03:38 UTC  
> Url: https://github.com/boostorg/beast/issues/17#issuecomment-221733616  

I hadn't actively planned it but I can probably cobble together a URI parser similar to the nodejs URI parser. I will add to the TODO. If having such a feature sooner rather than later will encourage the use of Beast and growth in its active user base, I would be quite motivated!

---

## Comment 3

> Username: bitbugprime  
> Created at: 2016-05-25 23:10:04 UTC  
> Url: https://github.com/boostorg/beast/issues/17#issuecomment-221734643  

One thing to consider if you do that is partial URL handling. Most URI parsers require a full "http://blahblah" thing or they'll barf. You don't get that with an HTTP GET, you only have the "/path/?key=value" stuff (the resource part of the URL).

---

## Comment 4

> Username: vinniefalco  
> Created at: 2016-05-25 23:15:06 UTC  
> Url: https://github.com/boostorg/beast/issues/17#issuecomment-221735472  

The nodejs parser parses the "scheme" if present, so I would include that as an option.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2016-06-12 15:18:37 UTC  
> Url: https://github.com/boostorg/beast/issues/17#issuecomment-225442566  

Update: There is now a CHANGELOG file:  
https://github.com/vinniefalco/Beast/blob/master/CHANGELOG

---

## Comment 6

> Username: bitbugprime  
> Created at: 2016-06-12 19:46:03 UTC  
> Url: https://github.com/boostorg/beast/issues/17#issuecomment-225456377  

The changelog mentions the new token_list,param_list stuff but the documentation doesn't really say what I use them for or how I use them. I also see you have both http::reason_string and http::status_text, which seem to do the same thing. Why have both, and is there one I should prefer?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2016-06-12 19:53:06 UTC  
> Updated at: 2016-06-12 20:50:04 UTC  
> Url: https://github.com/boostorg/beast/issues/17#issuecomment-225456718  

The documentation is a bit light on the rfc7230 helpers (`token_list`, `ext_list`, and `param_list`) so I've added a TODO to address it. As a stop gap I guess I could add example code to the javadoc. This is what it might look like:  
  
```  
    for(auto const token : token_list{"apple, pear, banana"})  
        std::cout << token << "\n";  
```  
  
Would output:  
  
```  
apple  
pear  
banana  
```  
  
`ext_list` operates similarly except that it allows semicolon delimited parameters. For example, `ext_list{"deflate;maxWindowBits=1,no_context_takeover"}` would produce a list of 1 item, where that item is a pair with `first` == `"deflate"` and the pair `second` == `param_list{";maxWindowBits=1,no_context_takeover"}`  
  
I believe `status_text` is obsolete, and I merely forgot to delete the file (its not listed in `<beast/http.hpp>`)

---

## Comment 8

> Username: vinniefalco  
> Created at: 2016-06-12 20:03:41 UTC  
> Url: https://github.com/boostorg/beast/issues/17#issuecomment-225457249  

The issues you brought up are partially addressed here (could use more in the HTML docs):  
https://github.com/vinniefalco/Beast/pull/24  
  
I'm going to close this, please open a new issue if you find another problem, thanks!
