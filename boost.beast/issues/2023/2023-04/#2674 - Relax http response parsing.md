# #2674 - Relax http response parsing? [Open]

> Username: BenKaufmann  
> Created at: 2023-04-26 18:24:02 UTC  
> Updated at: 2023-05-12 06:18:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2674  

I'm currently trying to replace a libsoup based http client implementation with boost beast.  
However,  beast's  http response parser is (deliberately) very strict when it comes to RFC compliance.   
For example,  (in contrast to libsoup) it only accepts:  
  
`status-line = HTTP-version SP status-code SP reason-phrase CRLF`  
  
Unfortunately, various "real world" servers  don't strictly play by the rules and e.g. return status lines that are only terminated by a `LF`.  While it would theoretically be the best to fix these servers, in practice this is often not really possible.  
  
I realize that this (or a similar) topic has already been discussed in the past:  
  
- https://github.com/boostorg/beast/issues/1761  
- https://github.com/boostorg/beast/issues/1138  
- https://github.com/boostorg/beast/issues/2344  
  
And of course, one could always just patch the parser code as needed.  Nevertheless, I'd like to know:  
  
- Are there any plans to provide a more relaxed way of parsing? E.g. maybe via some opt-in `basic_parser::relaxed()`  function? Or would something like this be strictly against the spirit of the library?  
- If adjusting parsing is not an option, what would be the best/easiest way to preprocess/filter incoming data first?  To me, the approach outlined in the `icy_stream.hpp` example seems overly complicated but so far I haven't found a simpler/more obvious approach.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-04-26 23:12:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2674#issuecomment-1524160989  

No, and the icy_stream example is the way.

---

## Comment 2

> Username: BenKaufmann  
> Created at: 2023-04-27 11:39:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2674#issuecomment-1525541418  

I see. That's unfortunate and makes switching to beast more complex.   
  
Out of curiosity: is there a particular (technical) reason for not following the suggestions from RFC-2616/section-19.3:  
> Clients SHOULD be tolerant in parsing the Status-Line and servers  
   tolerant when parsing the Request-Line. In particular, they SHOULD  
   accept any amount of SP or HT characters between fields, even though  
   only a single SP is required.  
  
>   The line terminator for message-header fields is the sequence CRLF.  
   However, we recommend that applications, when parsing such headers,  
   recognize a single LF as a line terminator and ignore the leading CR.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-04-27 13:15:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2674#issuecomment-1525678630  

> Out of curiosity: is there a particular (technical) reason for not following the suggestions from RFC-2616/section-19.3:  
  
Yes. That guidance was changed in rfc7230 which obsoletes rfc2616. Implementations still "may" be tolerant but it is noted that the practice exposes programs to vulnerabilities (see https://www.ietf.org/rfc/rfc7230.html#section-9.5).  
  
Also note that rfc9110 and rfc9112 obsolete rfc7230, but these have not been exhaustively applied to Beast. I'm willing to consider the possibility of allowing more tolerant parsing if the newer RFCs say something on the matter.

---

## Comment 4

> Username: BenKaufmann  
> Created at: 2023-04-27 14:39:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2674#issuecomment-1525818681  

Interesting, I didn't know that :+1:  I don't quite understand how rfc7230/section-9.5 (and 3.3.3) invalidates the more lenient approach to start-line and header field parsing mentioned in both rfc7230 (section-3.5) and rfc9112 (section-2.2), though.  
  
Anyhow, thanks for your quick response. I will then stick to patching the parser as I failed to get the `icy_stream` approach working with a `boost::beast::ssl_stream` in a reasonable amount of time.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2023-04-27 14:44:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2674#issuecomment-1525826219  

@klemens-morgenstern if you want to have a go at putting together a branch that has the more lenient parsing, I'd be open to it. We don't have any `parse_options` yet (a-la URL or JSON) but we could in theory add it.

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2023-05-10 23:29:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2674#issuecomment-1542931717  

Are you sure that's a good idea? That sounds to me like something that's more an area that http-proto would be customized on.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2023-05-12 06:17:44 UTC  
> Updated at: 2023-05-12 06:18:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2674#issuecomment-1545228979  

The reported issue correctly notes that clients may use a specific form of relaxed parsing. This basically means we _have_ to implement it if we want full compatibility.  
  
http-proto will have to do this too, please open a copy of this issue there.
