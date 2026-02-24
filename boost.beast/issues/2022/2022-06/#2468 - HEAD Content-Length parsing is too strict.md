# #2468 - HEAD Content-Length parsing is too strict [Closed]

> Username: ned14  
> Created at: 2022-06-21 17:55:35 UTC  
> Updated at: 2022-10-02 12:04:47 UTC  
> Closed at: 2022-10-02 12:04:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2468  

### Version of Beast  
  
322  
  
### Steps necessary to reproduce the problem  
  
```cpp  
         // Send the HTTP request  
         ::boost::beast::http::request<::boost::beast::http::empty_body> req(  
            ::boost::beast::http::verb::head, state->target, 11);  
         req.set(::boost::beast::http::field::host, {state->connection->host.data(), state->connection->host.size()});  
         req.prepare_payload();  
         OUTCOME_CO_TRY(co_await ::boost::beast::http::async_write(stream, req, make_token()));  
  
         // Read the HTTP response  
         ::boost::beast::flat_buffer                                              buffer;  
         ::boost::beast::http::response_parser<::boost::beast::http::empty_body> resp_parser(  
            ::boost::beast::http::status::unknown, 1);  
         ::boost::beast::http::response<::boost::beast::http::empty_body> resp;  
         // Apparently this is necessary to make Beast not puke on processing a HEAD response  
         resp_parser.skip(true);  
         result<size_t> r = co_await ::boost::beast::http::async_read_header(stream, buffer, resp_parser, make_token());  
         resp             = resp_parser.release();  
```  
  
` ::boost::beast::http::async_read_header()` still returns `error::bad_content_length` if the HEAD response contains a `Content-Length` exceeding the body size (which is zero).  
  
HTTP spec says:  
  
```  
   A server MAY send a Content-Length header field in a response to a  
   HEAD request ([Section 4.3.2 of [RFC7231]](https://datatracker.ietf.org/doc/html/rfc7231#section-4.3.2)); a server MUST NOT send  
   Content-Length in such a response unless its field-value equals the  
   decimal number of octets that would have been sent in the payload  
   body of a response if the same request had used the GET method.  
```  
  
Examples of this occurring in the wild:  
  
```  
curl --head https://gohugo.io  
HTTP/1.1 200 OK  
Age: 60131  
Cache-Control: public, max-age=0, must-revalidate  
Content-Length: 53138  
Content-Type: text/html; charset=UTF-8  
Date: Tue, 21 Jun 2022 01:05:11 GMT  
Etag: "42b17e0a6eb8d5b945b5609fa701f683-ssl"  
Link: </dist/app.bundle.js>; rel=preload; as=script, </dist/main.css>; rel=preload; as=style  
Referrer-Policy: origin-when-cross-origin  
Server: Netlify  
Strict-Transport-Security: max-age=31536000  
X-Content-Type-Options: nosniff  
X-Frame-Options: DENY  
X-Nf-Request-Id: 01G63P0HHDCD4X22DGQ87C9HNS  
X-Xss-Protection: 1; mode=block  
  
  
curl --head https://www.nedprod.com  
HTTP/1.1 200 OK  
Server: nginx/1.21.6  
Date: Tue, 21 Jun 2022 17:52:12 GMT  
Content-Type: text/html; charset=utf-8  
Content-Length: 183275  
Last-Modified: Sun, 19 Jun 2022 23:08:02 GMT  
Connection: keep-alive  
Vary: Accept-Encoding  
ETag: "62afac52-2cbeb"  
Strict-Transport-Security: max-age=15768000;  
X-Content-Type-Options: nosniff  
X-XSS-Protection: 1; mode=block  
X-Robots-Tag: none  
X-Download-Options: noopen  
X-Frame-Options: SAMEORIGIN  
X-Permitted-Cross-Domain-Policies: none  
Referrer-Policy: strict-origin  
Accept-Ranges: bytes  
```  
  
Proposed solution to https://github.com/boostorg/beast/blob/develop/include/boost/beast/http/impl/basic_parser.ipp#L833:  
  
```  
            if (existing.has_value())  
            {  
                if (v != *existing)  
                {  
                    if(f_ & flagSkipBody)  
                    {  
                        existing = v;  
                    }  
                    else  
                    {  
                        return bad_content_length();  
                    }  
                }  
            }  
            else  
            {  
                existing = v;  
            }  
```  
  
Potentially related: #2322  
  
**Edit:** Improved proposed solution.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-06-21 18:32:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2468#issuecomment-1162165683  

What is the difference between this issue and https://github.com/boostorg/beast/issues/2322?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-06-21 19:00:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2468#issuecomment-1162201168  

> What is the difference between this issue and #2322?  
  
The difference is that Niall correctly called `parser::skip( true )`. #2322 is user error.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-06-21 19:04:06 UTC  
> Updated at: 2022-06-27 21:10:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2468#issuecomment-1162204722  

@ned14 This test passes for me:  
```cpp  
    void  
    testIssue2468()  
    {  
        {  
            parser<false, empty_body> p(status::unknown, 1);  
            p.skip(true);  
            error_code ec;  
            p.put(buf(  
                "HTTP/1.1 200 OK\r\n"  
                "Content-Length: 42\r\n"  
                "\r\n\r\n"  
                    ), ec);  
            BEAST_EXPECTS(! ec, ec.message());  
            BEAST_EXPECT(p.is_header_done());  
            BEAST_EXPECT(p.is_done());  
        }  
        {  
            parser<false, empty_body> p(status::unknown, 1);  
            p.skip(true);  
            error_code ec;  
            p.put(buf(  
                "HTTP/1.1 200 OK\r\n"  
                "Age: 60131\r\n"  
                "Cache-Control: public, max-age=0, must-revalidate\r\n"  
                "Content-Length: 53138\r\n"  
                "Content-Type: text/html; charset=UTF-8\r\n"  
                "Date: Tue, 21 Jun 2022 01:05:11 GMT\r\n"  
                "Etag: \"42b17e0a6eb8d5b945b5609fa701f683-ssl\"\r\n"  
                "Link: </dist/app.bundle.js>; rel=preload; as=script, </dist/main.css>; rel=preload; as=style\r\n"  
                "Referrer-Policy: origin-when-cross-origin\r\n"  
                "Server: Netlify\r\n"  
                "Strict-Transport-Security: max-age=31536000\r\n"  
                "X-Content-Type-Options: nosniff\r\n"  
                "X-Frame-Options: DENY\r\n"  
                "X-Nf-Request-Id: 01G63P0HHDCD4X22DGQ87C9HNS\r\n"  
                "X-Xss-Protection: 1; mode=block\r\n"  
                "\r\n"  
                    ), ec);  
            BEAST_EXPECTS(! ec, ec.message());  
            BEAST_EXPECT(p.is_header_done());  
            BEAST_EXPECT(p.is_done());  
        }  
    }  
```  
  
If you call `parser::skip( true )`, then the logic for handling a response header unconditionally presumes that the message is complete regardless of what is in the Content-Length, or whether it is present. That happens here:  
https://github.com/boostorg/beast/blob/c2cff2254a903beb93ea5b434b000dca28f870c5/include/boost/beast/http/impl/basic_parser.ipp#L510  
  
It isn't clear to me how you're getting a different result.

---

## Comment 4

> Username: ned14  
> Created at: 2022-06-21 20:07:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2468#issuecomment-1162282793  

I think your repro might pass, but real world code does not perhaps.  
  
Here's the call stack:  
  
```  
basic_parser<0>::put() in case state_fields: calls parse_fields().  
parse_fields() iterates calling parse_field().  
parse_field() if f is field::content_length parses the field value and compares to body length.  
```  
  
I don't see `finish_header` in the call stack anywhere, and that's the code which gates on `flagSkipBody`.  
  
I guess the real question here is how is `basic_parser<0>::put()` getting called?  
  
It gets called from `http::detail::read_some_op`, that in turns gets called by `asio::detail::composed_op`.  
  
Is it possible that a different `parser.put()` overload is getting called and that skips the `flagSkipBody` check?

---

## Comment 5

> Username: ned14  
> Created at: 2022-06-21 20:23:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2468#issuecomment-1162300436  

I found the cause:  
  
In your repro, optional `existing` is unset.  
  
In my real world example, optional `existing` is set. This triggers the comparison of the `Content-Length` to the value of `existing`, and then `bad_content_length` gets returned.  
  
In `basic_parser<>::content_length_unchecked`, in your repro `f_` is 17. In my real world example, `f_` is 1553.  
  
That suggests there are two `Content-Length` fields being returned. I'm out of time today, have childcare to go do now, I'll look into it tomorrow and get back to you.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-06-21 23:19:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2468#issuecomment-1162453930  

Yes if there are two or more values for Content-Length, and they are different - the parser will generate an error. You keep saying "real world example" but you have not pasted the header which reproduces the defect.

---

## Comment 7

> Username: ned14  
> Created at: 2022-06-22 15:00:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2468#issuecomment-1163222638  

Yup, the cause was duplicate dissimilar `Content-Length` values returned in the HEAD response:  
  
```  
HTTP/1.1 200 OK  
Connection: keep-alive  
Content-Length: 0  
Last-Modified: Wed, 22 Jun 2022 14:47:36.1172144 UTC  
ETag: "1c340bae387ee00c581de49fda24b6ef"  
x-amz-meta-mdxmetadata-v1: KLUv/WBxAPUHAFZRNiFAaawDG/s7dIPA8VJc26H5UiX5bB0KrdsvhZh8ZxRXfE0sACwALgBH+vrBvm0+7fbHtvcaY7++rt5mc8qiCdt5mqW6/UCvNQh8gKBgsVfvZzpTA60g59VdP0yDrcx8uHpDewgwOrNpCGOjrKPGtNncUmX0iw3oaRvNKDaUnV15CgQvEApyYCSBSKSrM9Epkw0Z4lLdlKRkCUiJkkh7ddkLsiNlVxLz8oOZFxkhDbrKbIYTThhydDk1LqH36o1TlkRLdWVD2QcIjDNqafAXX4PKrFgYYVYoAAEMAFGJkfrAHajp0ULSRBvGuEfeTExdVFFICIww1VRygjoD  
Accept-Ranges: bytes  
Content-Length: 31  
```  
  
I've upcalled the bug with the server implementation.  
  
Duplicate `Content-Length` fields which don't match aren't unknown in real world HTTP. Stupid bugs like the above, obviously, but I've seen proxies do dumb things as well. Can I suggest:  
  
1. Add a new error code `multiple_content_length_mismatch` so this particular issue can be disambiguated from other causes of `bad_content_length`. In fact, several new error codes for disambiguating different causes of content length issues would be useful.  
2. Add a new flag to opt out of strict `Content-Length` parsing. I assume that in the Boost way you'll insist on strict input parsing only, but I'll ask anyway.  
3. In general, right now Beast's field parsing aborts further field processing if any one field is failed. What would be more useful when dealing with the real world internet is that it doesn't abort unless it really has to, but rather accumulates all the problems into an error buffer or something. Then one can write code to workaround broken servers and clients more easily.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-06-22 15:31:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2468#issuecomment-1163266514  

We can add additional errors (and source locations). I'm not entirely thrilled about accepting invalid inputs, even if it is configurable. I did allow this for JSON because there is not only an _overwhelming_  amount of non-compliant JSON but some of the most popular implementations allow it so I didn't have much of a choice.  
  
When it comes to HTTP however, the balance is in favor of strict parsing. In over 5 years you're the first to have this problem. I did some searching with Google and found several bug reports for various products where "Content-Length: 0" is erroneously slipped into the headers either as a duplicate or as an unexpected output where no Content-Length should have been present. In all cases the developers accepted this as a bug and fixed it. If Beast accepts this then it would be accepting inputs which are generally agreed upon to be defective...   
  
Parsing to the end of the HTTP fields even if an error occurs would be a significant change to the parser which I am not quite prepared to make now.

---

## Comment 9

> Username: ned14  
> Created at: 2022-06-23 13:45:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2468#issuecomment-1164427698  

More error codes to disambiguate different causes of failure are generally always a good thing.  
  
It's not always Content-Length: 0, sometimes you get two totally different values. I've seen this from time to time. What you do is compare the data received with the two values, and choose the one which matches :)  
  
I am surprised you found nothing on Google. I found a bunch of stuff last few days. The HTTP spec itself also makes reference to it occurring, as they bothered to describe the right way to handle it.  
  
I'm all for strict parsing as the default. Alas we don't have control often over what external others supply us. If you don't wish to change your parser, perhaps the ability to inject a "patcher" stage between the buffer fill and the parse might be an idea? It may need to stall the parse until all headers are received, then it can patch them, then release the parser. There are other use cases for such a facility e.g. rewriting malformed fields, where because Internet Explorer once accepted wrongness, all mainstream web browsers also accept wrongness, and then the code doing the wrongness never gets fixed.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2022-06-26 18:21:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2468#issuecomment-1166608155  

This has to go into 1.80.0

---

## Comment 11

> Username: alandefreitas  
> Created at: 2022-06-28 00:14:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2468#issuecomment-1168066678  

I'm not sure what we should do here for 1.80. I think @vinniefalco and @ned14 are still in disagreement about pretty much everything. Apart from the new `error_code` and the `patcher`, on which @vinniefalco didn't make any comments. In general, I think I mostly agree with @vinniefalco here. The initial issue makes it seems like this is only about the case where "the HEAD response contains a Content-Length exceeding the body size", where we would definitely have something to fix. On the other hand, accepting conflicting Content-Lengths is just bad:  
  
There's nothing to sanitize in such a response even if the library allows the user to continue. Especially if you get two different values and one of them is not `0`. Besides us not having examples of that in the wild yet, comparing the data received with the two values is very unreasonable because the data is simply not available yet. For Content-Lengths `A` and `B`, where `B > A`, then one would need _three_ parsers to first parse `A` bytes and then branch and parse `B - A` bytes as _both_ a continuation of `B` or the beginning of another message. It's hard to imagine many clients would do that, and that servers usually do that unless we have massive evidence this is very common.  
  
The extra `multiple_content_length_mismatch` error code seems less controversial. Vinnie didn't comment on that. But I'm wondering if this wouldn't be a breaking change for anyone who's not using `error_condition`s instead of `error_code`s, which is very common. And even if we assume everyone is using `error_condition`s, the new `error_code` would be useless anyway, because they would be in the same category. Meanwhile, the use case still seems _very_ rare: the server needs to have this problem _AND_ we need to treat this case separately _AND_ treating it separately is useful even when parsing has stopped (unless we implement both changes). I don't see a reasonable cost/benefit here.  
  
Vinnie also didn't comment on the optional "patcher", but it sounds like a variation of "I'm not entirely thrilled about accepting invalid inputs, even if it is configurable.". Even if this is less controversial, it looks like a big change and some work to come up with a reasonable stable API we are not going to regret, not to mention the controversial cost/benefit of accepting invalid inputs.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2022-06-28 00:45:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2468#issuecomment-1168084943  

I'm not enthused about storing a vector of error codes. I agree it is a weakness of the parser that it forces a "fail fast." It would be better to be able to read the entire header even with semantically invalid contents (as long as it is syntactically correct, as is the case here). And then notify the caller that the fields do not meet the syntactic requirements. This requires a rethinking of how the parser works, which I am reluctant to do in this release cycle. We should add the error code right away, `error::multiple_content_length` or something.  
  
The new Boost.HTTP.Proto should do better than Beast with respect to how it handles semantically invalid messages.

---

## Comment 13

> Username: madmongo1  
> Created at: 2022-06-28 07:59:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2468#issuecomment-1168372606  

My reading of 3.3.2 https://www.rfc-editor.org/rfc/rfc7230#section-3.3.2 suggests that the expectation is that there will be only one content-length field and that it will either contain a list of numbers that are the same or one number.  
  
Any proxy encountering a list of numbers MUST either reject the message or transform the field to contain one number.  
  
The text does not specify what would happen if two content-length headers are encountered, but it talks in terms of the singular in all cases.  
  
It is my view that accepting multiple content-length headers of different lengths would be an error and a security risk.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2022-06-28 15:07:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2468#issuecomment-1168848038  

> It is my view that accepting multiple content-length headers of different lengths would be an error and a security risk.  
  
Yeah, that's entirely valid. But, there is value in having a library that detects this condition, reports it to the user, but still makes a best-effort to deliver the complete HTTP message header to the caller so they may inspect what is there and take any further actions related to the business logic.. The default behavior could be simply to reply with a Bad Response, which mitigates the security risk. However the caller should be able to opt-in to different behavior.

---

## Comment 15

> Username: ned14  
> Created at: 2022-06-28 15:53:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2468#issuecomment-1168901574  

There appears to be an increasing pattern of me and Vinnie agreeing recently. The above is one such instance.  
  
Back to my original report, luckily the HTTP server in question was provided by an internal team, so I was able to persuade them to fix their HEAD implementation. However in the wild I've especially noticed incorrect HTTP when proxy servers get in the middle. I remember a particular corporate proxy for the entire worldwide firm which corrupted all HTTP going through it in a way which meant only Internet Explorer could reach the public internet. I was never sure if that wasn't actually intentional rather than plain incompetence.
