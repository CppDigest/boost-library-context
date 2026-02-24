# #1897 - Inapropriate error::body_limit, parsing chunked and multipart reponses [Closed]

> Username: ErikvLinstee  
> Created at: 2020-04-09 13:09:43 UTC  
> Updated at: 2020-08-20 13:27:55 UTC  
> Closed at: 2020-06-01 16:13:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1897  

I am using beast with boost 1.72.  
  
This is what I think is wrong.  
1. body limit is calculated incorrectly  
2. body limit is calculated when it should not  
  
About 1, body limit is calculated incorrectly:  
The calculation of the body limit does not only count the newly received data, but also the old data not yet processed because of a need_more.  
  
This can be observed by adding a counter to on_body_impl of a custom parser . Have it add the size of the string_view and return need_more.  
  
```  
std::size_t on_body_impl(beast::string_view s,  
  beast::error_code& ec) {  
  
  counter_ += s.size();  
  ec = beast::http::error::need_more;  
}  
```  
  
You will receive error::body_limit around the time the counter_ has grown to the body_limit. Clearly this is wrong, because the actual body size at that time is s.size().  
Every time you return need_more, newly received data is added to s, but the counter is counting the entire size of s, counting the previously received data again and again.  
  
  
About 2, body limit is calculated when it should not  
  
I am using a custom parser to receive HTTP responses that continuously send status information, or motion JPEGs.  
None of the data read by async_read will be actually added to the message body of the parser, but still error::body_limit will eventually fire.  
  
Here is an example of a web server sending a multipart response.  
http://88.53.197.250/axis-cgi/mjpg/video.cgi?resolution=320x240  
It's a rather slow site, so it takes a while to exceed the default limit.

---

## Comment 1

> Username: ErikvLinstee  
> Created at: 2020-04-09 13:18:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-611523155  

Could it be that on_body_impl needs to return s.size() even when ec is set to need_more?

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-04-09 13:20:55 UTC  
> Updated at: 2020-04-09 13:21:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-611524286  

Hi @ErikvLinstee,  
  
Thanks for raising this.  
  
Just so I am clear.  
  
What I think I understand is that:  
  
1. You are receiving a mime-multipart response of essentially unlimited (composite) length?  
2. You are seeking to limit the size of each mime part?  
3. You want to discount the lengths of previously processes mime parts in the computation of body_length?  
  
Is that correct?

---

## Comment 3

> Username: ErikvLinstee  
> Created at: 2020-04-09 13:32:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-611529989  

> Could it be that on_body_impl needs to return s.size() even when ec is set to need_more?  
  
Hmm, no, that is definitely not it.

---

## Comment 4

> Username: ErikvLinstee  
> Created at: 2020-04-09 13:36:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-611531888  

> Hi @ErikvLinstee,  
>   
> Thanks for raising this.  
>   
> Just so I am clear.  
>   
> What I think I understand is that:  
>   
>     1. You are receiving a mime-multipart response of essentially unlimited (composite) length?  
  
Indeed. Either that, or a chunked.  
  
>   
>     2. You are seeking to limit the size of each mime part?  
  
No, I am processing the mime parts in my custom parser. I can deal with them there.  
  
>   
>     3. You want to discount the lengths of previously processes mime parts in the computation of body_length?  
  
Yes. As long as the connection is alive, the parser seems to be adding everything to the body length. But it isn't even doing that, it is adding the same data multiple times when need_more is in play.

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-04-09 13:38:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-611533106  

I see. Thanks.  
  
Do you have a workaround for the moment? I'm right in the middle of fixing another issue.

---

## Comment 6

> Username: ErikvLinstee  
> Created at: 2020-04-09 13:42:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-611535110  

> I see. Thanks.  
>   
> Do you have a workaround for the moment? I'm right in the middle of fixing another issue.  
  
No, not yet. There does not seem to be a way to reset the calculation or disable it from the API. I will have to dive into the source and see if I can fix it there. That will probably take a while.

---

## Comment 7

> Username: madmongo1  
> Created at: 2020-04-09 14:01:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-611544552  

Could you set the count to std::numeric_limits<std::size_t>::max() and then maintain your own counter in your parser?  
  
You are quite at liberty to set ec to anything you want to prevent further parsing/comms.

---

## Comment 8

> Username: ErikvLinstee  
> Created at: 2020-04-09 14:47:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-611568698  

> Could you set the count to std::numeric_limitsstd::size_t::max() and then maintain your own counter in your parser?  
  
Which count is that?  
  
I do not need a counter in my parser, that was only an example to illustrate the problem.  
  
I have added the relevant part of the parser below. Not shown here is on_body_init_impl. Suffice it to say that it parses the content type and if it is multipart it determines the boundary and sets the state_ to multipart. If it is not multipart, it initialises the body reader to the content length. The reader has been constructed with the base and body of the parser.  
  
```  
beast::http::string_body::reader rd_ {get().base(), get().body()};  
```  
  
This will either simply add s to the body, or it will parse the multipart data.  
In the latter case, it will ignore the preamble and epilogue and it will pass the parts between the boundaries to the client code.  
The parts will NOT be added to the body, but the base class response_parser will add s.size() it to the count of the body length. Again and again, until body_limit is reached.  
  
```  
std::size_t custom_parser::on_body_impl(  
    beast::string_view s,  
    boost::system::error_code& ec)  
{  
    /**  
     * multipart body  
     *  
     * body = [ preamble CRLF] "--" boundary *(part encapsulating-boundary) part closing-boundary ignore  
     * part = [ headers ] CRLF part-body  
     * encapsulating-boundary = CRLF "--" boundary  
     * closing-boundary = CRLF "--" boundary "--"  
     */  
  
    BOOST_ASSERT(state_ != state::complete);  
  
    auto const MAX_PREAMBLE = 1000;  
    auto const MAX_PART = 1'000'000;  
  
    auto p = static_cast<char const*>(s.data());  
    auto n = s.size();  
    auto const p0 = p;  
    auto const p1 = p0 + n;  
    beast::string_view part {};  
    bool is_last {false};  
  
loop:  
    switch(state_) {  
    case state::nothing_yet:  
        state_ = state::not_multipart;  
        BOOST_FALLTHROUGH;  
  
    case state::not_multipart:  
        return rd_.put(boost::asio::buffer(  
                           s.data(), s.size()), ec);  
  
    case state::multipart:  
        if(n == 0) {  
            ec = beast::http::error::need_more;  
            return 0;  
        }  
        state_ = state::preamble;  
        BOOST_FALLTHROUGH;  
  
    case state::preamble:  
  
        // This will leave p at the start of  
        // the first body part if no error  
        // occurs.  
        parse_preamble(p, p + (std::min<std::size_t>)(  
                           MAX_PREAMBLE, n), multipart_boundary_, ec);  
  
        if (ec) {  
            if(ec == beast::http::error::need_more) {  
            }  
            goto done;  
        }  
  
        state_ = state::part;  
  
        if(p >= p1) {  
            ec = beast::http::error::need_more;  
            goto done;  
        }  
        n = static_cast<std::size_t>(p1 - p);  
        BOOST_FALLTHROUGH;  
  
    case state::part:  
        parse_part(p, p + (std::min<std::size_t>)(  
                       MAX_PART, n), part, is_last, multipart_boundary_, ec);  
  
        if (ec) {  
            goto done;  
        }  
  
        if (part_handler_) {  
            ec = error::multipart_part;  
            if (is_last) {  
                ec = error::last_multipart_part;  
            }  
  
            part_handler_(part, ec);  
            ec = {};  
        }  
  
        if (!is_last) {  
            break;  
        }  
        state_ = state::epilogue;  
        BOOST_FALLTHROUGH;  
  
    case state::epilogue:  
        p = p1;  
        goto done;  
  
    case state::complete:  
        ec = {};  
        goto done;  
    }  
    if (p < p1) {  
        n = static_cast<std::size_t>(p1 - p);  
        goto loop;  
    }  
done:  
    return static_cast<std::size_t>(p - p0);  
}  
```

---

## Comment 9

> Username: madmongo1  
> Created at: 2020-04-09 15:17:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-611585469  

Thanks very much for the detailed explanation. What you are saying is clear now.  
  
In effect, the concept of body_size is ambiguous when parsing a mime-multipart message of inderminate size.  
  
i.e.   
* should it include multi-part framing or not?  
* Should it be the aggregate of all parts or not?  
  
I'll need to take this away and think about what the correct design is here.  
  
Currently the body_size is the total number of bytes seen in the body. The current default limit is SIZE_T_MAX (which is a very big number on a 64-bit system, not so big on a 32 bit system).  
  
Once again, many thanks.

---

## Comment 10

> Username: ErikvLinstee  
> Created at: 2020-04-09 17:08:19 UTC  
> Updated at: 2020-04-10 10:55:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-611642221  

> Thanks very much for the detailed explanation. What you are saying is clear now.  
  
I don't feel it is entirely.  
I will try to explain.  
  
>   
> In effect, the concept of body_size is ambiguous when parsing a mime-multipart message of inderminate size.  
  
I would say the body_size is ambiguous any time a content-length is not specified. When a content-length is not specified, it might mean that there is no actual end to the content. A server may keep the connection open to send data indefinitely. It might frame the data using a multipart content-type, or it might frame it in chunks, or it might even define its own kind of framing.  
  
The point is, in such a case the body_limit is meaningless and should not be enforced by the parser. Only the client ultimately receiving the data can decide when it has enough.  
  
>     * should it include multi-part framing or not?  
  
Only if content-length has been specified.  
  
>     * Should it be the aggregate of all parts or not?  
  
Only if content-length has been specified.  
  
> I'll need to take this away and think about what the correct design is here.  
  
A few observations:  
  
1. If content-length is specified, the parser should never parse more than content-length or body_limit, which ever comes first. Works as advertised and does not require change.  
  
2. If content-length is not specified, there are two options. The stream is supposed to be endless, or it is not. The parser can not decide.  
  
3. If the stream is not endless, a body_limit makes sense.  
  
4. If the stream is endless, a body_limit does not make sense.  
  
5. But if it is endless, without a body_limit it might eventually run out of buffer space. This is a problem and must be prevented.  
  
This leads me to the following:  
A. because of 4 it must be possible to set body_limit to unlimited,  
B. because of 5 it must be an error to receive a body with no content-length and body_limit set to to unlimited.  
C. whoever needs a parser that can deal with an unlimited body, should be able to create a custom parser that overrides the error of B.  
D. setting body_limit to unlimited on a response with a content-length effectively resets body_limit to content-length.  
  
>   
> Currently the body_size is the total number of bytes seen in the body.  
  
The body_size may be, but the calculation of body_limit works differently.  
  
Note that in parse_body_to_eof body_limit_ is decremented with n, regardless of how many n on_body_impl actually processes. If on_body_impl does not process them all, because it need_more, the same unprocessed n will be subtracted from body_limit_ again when the parser loops back to parse_body_to_eof. This means body_limit_ is calculated incorrectly regardless.  
  
```  
template<bool isRequest>  
void  
basic_parser<isRequest>::  
parse_body_to_eof(char const*& p,  
    std::size_t n, error_code& ec)  
{  
    if(n > body_limit_)  
    {  
        ec = error::body_limit;  
        return;  
    }  
    body_limit_ = body_limit_ - n;  
    ec = {};  
    n = this->on_body_impl(string_view{p, n}, ec);  
    p += n;  
    if(ec)  
        return;  
}  
```

---

## Comment 11

> Username: ErikvLinstee  
> Created at: 2020-04-10 10:05:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-611968703  

Attached is a patch to Boost 1.72's version of Beast that changes body_limit to optional.  
  
[beast-1.72.patch.txt](https://github.com/boostorg/beast/files/4461035/beast-1.72.patch.txt)

---

## Comment 12

> Username: madmongo1  
> Created at: 2020-04-10 17:55:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-612144902  

Thanks for taking the time to do this. I'll discuss with Vinnie.

---

## Comment 13

> Username: ErikvLinstee  
> Created at: 2020-04-10 18:04:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-612148549  

The patch below adds to the previous one the changes required to do B and C.  
  
It does this by checking if a body_limit is set if the headers do not specify a content-length. If no limit is set, it is considered an error::body_limit.  
To make this stick I had to remove `ec = {};` from parser's on_header_impl. That was superfluous anyway, because ec was already cleared just before on_header_impl.  
  
This change allows a custom parser to decide if it wants to honor the error::body_limit, or if it wants to continue anyway. It forces the programmer to make a conscious decision about this situation.  
  
I have also fixed what I think was a bug. In basic_parser::put there was no `if (ec) goto gone` after `finish_header` like in all the other places where ec is being used to detect errors.  
You will see that without it, a response with content-length set higher than body_limit, goes unnoticed. Even though the error is set in finish_header.  
  
[beast-1.72.patch.txt](https://github.com/boostorg/beast/files/4462361/beast-1.72.patch.txt)

---

## Comment 14

> Username: vinniefalco  
> Created at: 2020-04-10 21:42:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-612230856  

From what I can see at the tip of develop, the parser does not count chunked body bytes towards the limit. I think it should, here:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/http/impl/basic_parser.ipp#L729

---

## Comment 15

> Username: ErikvLinstee  
> Created at: 2020-04-11 12:41:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-612412371  

You are right, it should. But only what is actually being used. So the n returned by on_chunk_body_impl, not the n passed to parse_chunk_body. Because otherwise it would be added more than once. If on_chunk_body_impl is passed n=10 for instance, and it returns 6, the remaining 4 will again be passed to on_chunk_body_impl on the next loop.  
  
Note that in response_parser, none of the data is actually added to the body. Unless one does so explicitly in response_parser's on_chunk_body callback.

---

## Comment 16

> Username: vinniefalco  
> Created at: 2020-04-11 15:08:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-612441974  

Note for the HTTP Refactor: The direct access to chunking interface should be lower-level than `parser`.

---

## Comment 17

> Username: ErikvLinstee  
> Created at: 2020-04-11 18:45:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-612487908  

Here's an extra challenge. Considering that RTSP largely follows the same message structure as HTTP, would there be some way to find a common base for parser? With specialisations for HTTP and RTSP?

---

## Comment 18

> Username: vinniefalco  
> Created at: 2020-04-11 22:05:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-612522893  

>would there be some way to find a common base for parser  
  
I think for the Great HTTP Refactor, that selected parse algorithms can be made into free functions or subunits, and these might be reused for RTSP.

---

## Comment 19

> Username: vinniefalco  
> Created at: 2020-04-24 17:39:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-619152724  

We need a unit test first to confirm that body_limit is not respected for chunked bodies, and then a fix.

---

## Comment 20

> Username: vinniefalco  
> Created at: 2020-05-06 13:38:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-624654954  

FYI there is nothing ambiguous about "body size" - it is simply the total number of bytes in the Content Body, which is a well-defined term in the HTTP RFC.

---

## Comment 21

> Username: ErikvLinstee  
> Created at: 2020-05-06 13:45:49 UTC  
> Updated at: 2020-05-06 13:46:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-624658931  

You are correct. Body size is well defined. It is just that setting a limit on body size is not always appropriate.

---

## Comment 22

> Username: madmongo1  
> Created at: 2020-05-26 12:27:15 UTC  
> Updated at: 2020-05-26 12:52:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-633993020  

> We need a unit test first to confirm that body_limit is not respected for chunked bodies, and then a fix.  
  
Added this test. it passes. body_limit_ is decremented by the parse_chunk_header code once it knows the length of the chunk.  
  
```  
    void testChunkedBodySize()  
    {  
        string_view resp =   
            "HTTP/1.1 200 OK\r\n"  
            "Server: test\r\n"  
            "Transfer-Encoding: chunked\r\n"  
            "\r\n"  
              
            // chunk 1  
            "4\r\n"  
            "Wiki\r\n"  
              
            // chunk 2  
            "5\r\n"  
            "pedia\r\n"  
              
            // chunk 3  
            "E\r\n"  
            " in\r\n"  
            "\r\n"  
            "chunks.\r\n"  
              
            // end  
            "0\r\n"  
            "\r\n";  
  
        {  // body limit not exceeded  
            response_parser<string_body> p;  
            p.eager(true);  
            p.body_limit(23);  
            error_code ec;  
            p.put(net::buffer(resp.data(), resp.size()), ec);  
            BEAST_EXPECTS(ec.message() == "Success", ec.message());  
            p.put_eof(ec);  
            BEAST_EXPECTS(ec.message() == "Success", ec.message());  
        }  
          
        {  // body limit exceeded  
            response_parser<string_body> p;  
            p.eager(true);  
            p.body_limit(22);  
            error_code ec;  
            p.put(net::buffer(resp.data(), resp.size()), ec);  
            BEAST_EXPECTS(ec.message() == "body limit exceeded", ec.message());  
            p.put_eof(ec);  
            BEAST_EXPECTS(ec.message() == "partial message", ec.message());  
        }  
    }  
```

---

## Comment 23

> Username: vinniefalco  
> Created at: 2020-05-26 12:39:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-633998738  

indentation

---

## Comment 24

> Username: madmongo1  
> Created at: 2020-05-26 12:53:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-634005541  

> indentation  
  
better?

---

## Comment 25

> Username: madmongo1  
> Created at: 2020-05-26 15:57:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-634115749  

@ErikvLinstee ,  
  
Thanks for your patience in this matter.  
  
I have finally realised the problem here. Your sub-parser is returning the error_code `need_more` when it needs more data to complete a segment of the mime-multipart parse.   
  
This error code is not designed for this use case. If the parser has passed you bytes it expects you to consume them all. This might imply allocating and managing storage within your mime parser.  
  
Please let me know whether this can work for your use case. If not I'm happy to work with you to either identify a workaround or a small internal API change.  
  
Your idea of making the body_limit optional is reasonable (as long as it defaults to protection: on), but actually I think an orthogonal concern to the issue of double-counting you have experienced.

---

## Comment 26

> Username: ErikvLinstee  
> Created at: 2020-05-26 17:48:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-634176394  

> I have finally realised the problem here. Your sub-parser is returning the error_code need_more when it needs more data to complete a segment of the mime-multipart parse.  
  
Correct.  
  
> This error code is not designed for this use case. If the parser has passed you bytes it expects you to consume them all. This might imply allocating and managing storage within your mime parser.  
  
But then the question would be, why does on_body_impl return a size_t, or anything at all, if it expects you to consume everything?  
As it stands it works almost perfectly, except for the wrong n being subtracted from body_limit_. It seems to me need_more works as advertised.  
  
  
> Please let me know whether this can work for your use case. If not I'm happy to work with you to either identify a workaround or a small internal API change.  
  
Allocating an managing storage inside the sub-parser would of course be possible, but it would complicate matters a lot and the extra copying of data would needlessly reduce performance.  
I would strongly suggest going with my proposal. The only real change is moving the subtracting 2 lines down like this:  
```  
    body_limit_ = body_limit_ - n;  
    ec = {};  
    n = this->on_body_impl(string_view{p, n}, ec);  
    p += n;  
```  
```  
    ec = {};  
    n = this->on_body_impl(string_view{p, n}, ec);  
    body_limit_ = body_limit_ - n;  
    p += n;  
```  
Nothing else seems to be affected.  
  
> Your idea of making the body_limit optional is reasonable (as long as it defaults to protection: on),  
  
Exactly what I had in mind.  
  
> but actually I think an orthogonal concern to the issue of double-counting you have experienced.  
  
That is correct, they are separate issues. Which is why I did not include the optional body_limit in my explanation above about moving the subtraction a few lines down.

---

## Comment 27

> Username: madmongo1  
> Created at: 2020-05-28 13:47:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-635360852  

@ErikvLinstee   
  
I have some answers.  
  
> But then the question would be, why does on_body_impl return a size_t, or anything at all, if it expects you to consume everything?  
  
Because there is (more than one) bug in the parser/body/reader API  
  
> As it stands it works almost perfectly, except for the wrong n being subtracted from body_limit_. It seems to me need_more works as advertised.  
  
If we are to support the concept of partial success of parsing the body, we are going to have to do major surgery to the parser class. Of particular concern is when the message encoding is chunked.  
  
At present, if `reader::put` returns a value less than the byte count of the buffer sequence passed to it, it will cause the parser to remain in the "chunk body" state. The next chunk header will then be sent into the body reader as body data. After this, all bets are off. For now, we will have to consider a partial success of body reader as an unsupported use case. My apologies.  
  
> Allocating an managing storage inside the sub-parser would of course be possible, but it would complicate matters a lot and the extra copying of data would needlessly reduce performance.  
  
Unfortunately at this time, it's the best solution I can offer. HTTP is scheduled to be refactored, and the parser is one area where a number of design issues have been identified by field testing (such as yours).  
  
Accomodating this change would essentially require a rewrite of the parser, with associated risks to existing users, for very limited gain given the imminent major refactor.  
  
>> Your idea of making the body_limit optional is reasonable (as long as it defaults to protection: on),  
  
> Exactly what I had in mind.  
  
I will push a patch for this as it's an easy win which will not impact existing users. It will be "opt-in" which means you'll have to explicitly request no body limit [safe by default principle].

---

## Comment 28

> Username: ErikvLinstee  
> Created at: 2020-05-28 14:47:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-635397175  

> At present, if reader::put returns a value less than the byte count of the buffer sequence passed to it, it will cause the parser to remain in the "chunk body" state. The next chunk header will then be sent into the body reader as body data. After this, all bets are off.  
  
Ah, I see. I did not look at this.  
So you are saying that if a sub parser returns a partial result when receiving a chunked body, there will be trouble? With or without the change I suggested?  
  
> Accomodating this change would essentially require a rewrite of the parser, with associated risks to existing users, for very limited gain given the imminent major refactor.  
  
I understand that you don't want to make major changes before refactoring. Making me think this will be soon, no?  
  
I don't understand your comment about it requiring a rewrite. The change I suggested is a very small one and would not make matters worse, right? Or only for sub parsers returning a partial result, which they should not be doing?  
  
Anyway, I can live without the change to the main code, as I can and have changed that locally anyway. I just have to be careful to reapply it should I update the source.  
  
Hopefully, the refactored code will be able to deal with it. Any thoughts on a time table for the refactoring?

---

## Comment 29

> Username: madmongo1  
> Created at: 2020-05-28 14:56:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-635402658  

> So you are saying that if a sub parser returns a partial result when receiving a chunked body, there will be trouble? With or without the change I suggested?  
  
Yes  
  
> Hopefully, the refactored code will be able to deal with it.   
  
Yes I have some thoughts on how a new simplified parser/handler relationship might look. I'm sure Vinnie does too.  
  
> Any thoughts on a time table for the refactoring?  
  
I think your motivating use case just accelerated the timetable. Previously we merely felt internal discomfort that the parser was not perfectly correct, which was not sufficient motivation to invest time. Now it's impacting a user, which changes the game.  
  
@vinniefalco will have the final say, but it's something we discuss fairly often.

---

## Comment 30

> Username: ErikvLinstee  
> Created at: 2020-05-28 15:30:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-635422083  

> I think your motivating use case just accelerated the timetable.  
  
I am so sorry :-)

---

## Comment 31

> Username: ErikvLinstee  
> Created at: 2020-05-30 12:43:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-636326011  

I was wondering why you changed the API?  
Why not add body_limit()?  
  
It seems to me that now everyone has to change their code, because of the API change, whereas no-one would have to if you were to simply add body_limit without an argument and have that reset the body limit.

---

## Comment 32

> Username: vinniefalco  
> Created at: 2020-05-30 14:32:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-636338970  

> It seems to me that now everyone has to change their code, because of the API change  
  
Why does existing code have to change?

---

## Comment 33

> Username: ErikvLinstee  
> Created at: 2020-05-30 15:02:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-636342535  

Because the API changed?  
  
body_limit will now take an optional<std::uint64_t>, instead of a std::uint64_t. I guess you are counting on implicit conversion to optional?

---

## Comment 34

> Username: vinniefalco  
> Created at: 2020-05-30 15:20:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1897#issuecomment-636345601  

> I guess you are counting on implicit conversion to optional?  
  
Yes. Existing code should continue to work, and new code can just pass `boost::none` to indicate no limit. The function signature `body_limit() const` with no parameters, would be expected to return the limit not to set it to no limit.
