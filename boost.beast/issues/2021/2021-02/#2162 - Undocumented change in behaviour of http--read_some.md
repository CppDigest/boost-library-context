# #2162 - Undocumented change in behaviour of http::read_some [Closed]

> Username: genossefloyd  
> Created at: 2021-02-11 12:44:16 UTC  
> Updated at: 2021-03-03 18:08:50 UTC  
> Closed at: 2021-03-03 17:56:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2162  

We did an update of boost 1.73 to 1.75 and have been confronted with failing unitests when reading HTTP requests or responses sometimes. The problems have been a call of BOOST_ASSERT in "basic_parser.ipp" and a stuck test as it was trying to read from a stream with no further data. In both cases I could break down the problem to a removed test of "parser.is_done()" in - apperently in scope of PR #1957.  
  
We could solve our problems by adding manual checks of the parser state before calling the read_some function. However,   
I am not sure if this change was intended - hence this report.  
  
If this change was unintended, you can treat it as a bug. If not, a remark in the changelog would have been helpful and would have safed quite some time of investigation.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-02-11 12:48:27 UTC  
> Updated at: 2021-02-11 12:49:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2162#issuecomment-777431086  

HI @genossefloyd ,  
  
Thanks for reporting. Would you mind confirming whether it is one of our tests that it failing or one of yours?  
  
If we have missed a test case I'd like to capture it. Are you able to supply more details?

---

## Comment 2

> Username: genossefloyd  
> Created at: 2021-02-11 12:52:04 UTC  
> Updated at: 2021-02-11 12:55:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2162#issuecomment-777433316  

It have been our unittests. One was testing the server behaviour for a request with invalid body - in which case the further processing was aborted. The other test stuck when processing a request without body.  
  
The problem must not be a bug on your side and could be seen as incorrect usage. However, the code was working with the old boost version for several months and just failed because of the update. And the problem source was not so easy to detect.

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-02-11 13:15:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2162#issuecomment-777448426  

I understand.  
  
Any test data/code you can share with me will help me to understand whether this is an unintended regression or whether the invalid use was your code invoking undefined behaviour.  
  
If it's cheap to trap usage errors, I'd prefer to do it.

---

## Comment 4

> Username: genossefloyd  
> Created at: 2021-02-11 15:20:22 UTC  
> Updated at: 2021-02-11 15:29:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2162#issuecomment-777570488  

I can not really share code, as it is not open and far to complex (some nested template stuff for different kind of protocol layers). But what we basically do on the server side (very simplified) is:  
```  
boost::beast::tcp_stream stream;  
boost::beast::http::request_parser<Impl> parser;  
boost::beast::flat_static_buffer<8192> buffer;  
boost::beast::error_code ec;  
  
boost::beast::http::read_header(stream, buffer, parser, ec);  
if (ec)  
{  
    // handle error/end of stream  
}  
else  
{  
    //do stuff with header  
    size_t bodyCount = 0;  
    do  
    {  
        boost::beast::http::read_some(stream, buffer, parser, ec);  
        if(ec)  
            //handle error  
        else  
            //do stuff with body parts  
     } while (!parser.is_done());  
}  
```  
So, one problem here is, that for a request without body (in our case a DELETE request) it is not returning from read_some(...).  
And the other is, if 'buffer' still holds some data - which happens in one of our error cases with an invalid body - inside of read_some(...) the parser function "put" is called, which has an BOOST_ASSERT(state != state::complete). This caused some fatal crashes in our unittests. I think this is quite radical and I would rather expect an exception for such an "user error".  
  
I solved the problem by simply adding a "if (parser.is_done())" check, but it would be more user-friendly if you could catch this case inside and provide an error code.

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-02-15 14:24:23 UTC  
> Updated at: 2021-02-15 14:24:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2162#issuecomment-779255112  

@genossefloyd After some discussion with @vinniefalco we're of the view that the lockup is a user-code problem.  
  
In the code snippet above, read_some is called prior to checking whether `parser.is_done()`. For (say) a `DELETE` request where the client does not set the `Content-Length` header or chunk-encode the request, the parser assumes the request is done as soon as the HTTP header section has been successfully parsed.  
  
In your case you would need to change the `do` loop to a `while` loop:  
  
```  
    while (!parser.is_done())  
    {  
        boost::beast::http::read_some(stream, buffer, parser, ec);  
        if(ec)  
            //handle error  
        else  
            //do stuff with body parts  
     }  
```

---

## Comment 6

> Username: genossefloyd  
> Created at: 2021-02-18 06:25:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2162#issuecomment-781089636  

@madmongo1   
It is ok to treat this as an usage error. In this case I would highly suggest to update your documentation. Besides, there is now a chance to run into the BOOST_ASSERT in include/boost/beast/http/impl/basic_parser.ipp line 85. In our case this caused an untreatable termination. Throwing an exception instead would be of much help.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2021-02-18 06:56:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2162#issuecomment-781103149  

I agree, there should be no undefined behavior in the parser. Asserting on a precondition violation produces undefined behavior.

---

## Comment 8

> Username: madmongo1  
> Created at: 2021-02-18 07:01:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2162#issuecomment-781105711  

A couple of questions:  
  
Did the assertion happen as a result of correct use?  
  
what are the specific actions I should take?

---

## Comment 9

> Username: genossefloyd  
> Created at: 2021-02-18 08:59:21 UTC  
> Updated at: 2021-02-18 09:04:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2162#issuecomment-781191055  

The problem occured in our code when we processed an http request with semantically invalid http body - which is processed sequentially as it comes in and not as a whole. There was a call of read_some with the parser being in state "complete" and the buffer still containing data. Therefore, we ran into the named assertion, that has caused an application termination.   
  
The reason for the recurring read while the parser was done, is not entirely clear to me, as it is quite convoluted code and I did not write myself, but I have fixed this also by adding a manual check for the parser state and I accept it as an usage error. However,  the assert is a quite radical operation and I just ask for some treatable error response.  
  
A more user-friendly error handling would be nice. I suggest either to replace the assert with an exeception to be thrown or add a check in a higher interface level (maybe directly in the read_some(...) implementation), that catches this case and returns an error.  
After all, this check was present up to version 1.73!

---

## Comment 10

> Username: madmongo1  
> Created at: 2021-03-03 17:56:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2162#issuecomment-789936363  

continued in #2172

---

## Comment 11

> Username: madmongo1  
> Created at: 2021-03-03 18:08:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2162#issuecomment-789945061  

@genossefloyd    
  
Please be advised of the decision I have taken in #2172.  
  
Removing the assertion would remove a very powerful means of detecting irrecoverable logic errors. No correct program will call `put()` when the parser `is_done()`. An explanation has been added annotating the code at the point of the assertion.  
As of boost 1.76, if the code is compiled with `BOOST_ASSERT` set to be a no-op, then the function will return `error::parser_stale` in the `error_code`.  
This will not be specifically documented as it is not something that is ever expected to occur in a correct program. Think of it as a last-ditch attempt to help you find the logic error if you don't have test coverage.  
  
The (admittedly not 100%) consensus was that to introduce an exception here would add an extra undocumented control flow to a library that in general does not throw. While this may help you, my concern is that it might become a source of contention for other users.  
  
Thanks very much for reporting the issue and engaging with us. It's very much appreciated.
