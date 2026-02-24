# #386 - Can not beast::http::read with boost::asio::streambuf [Closed]

> Username: qwertzui11  
> Created at: 2017-05-24 14:16:19 UTC  
> Updated at: 2017-06-12 10:55:56 UTC  
> Closed at: 2017-06-12 10:50:17 UTC  
> Url: https://github.com/boostorg/beast/issues/386  

`beast::http::read(stream, cache, parser);`  
does not compile with   
```  
using cache_type = boost::asio::streambuf;  
cache_type streambuf;  
beast::buffers_adapter<cache_type::mutable_buffers_type> cache(streambuf.prepare(512));  
```  
but I compiles with  
`boost::asio::mutable_buffer cache;`  
According to the documentation `buffers_adapter` takes a `MutableBufferSequence` which `boost::asio::streambuf::prepare` fullfills and wraps it to a `DynamicBuffer`.   
Have I missunsterstood something?  
Im using gcc6.1 on ubuntu 17.04.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-05-24 15:46:57 UTC  
> Url: https://github.com/boostorg/beast/issues/386#issuecomment-303765879  

Hmm... I think what you're doing should work, but I have not tried it. And there could very well be a bug. I will look into it right away!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-05-24 16:30:07 UTC  
> Updated at: 2017-05-24 16:30:17 UTC  
> Url: https://github.com/boostorg/beast/issues/386#issuecomment-303778572  

I tried the code you provided and it compiles for me: https://github.com/vinniefalco/Beast/commit/f9db7676beff74a908dff1df7c137ec7b658833b  
  
Do you have any more information? Can you include the output containing the compilation error? What version of Boost? What version of Beast? Thanks!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-05-24 16:46:05 UTC  
> Url: https://github.com/boostorg/beast/issues/386#issuecomment-303782957  

Hmm... the code compiles correctly under gcc6.x  
https://travis-ci.org/vinniefalco/Beast/jobs/235713266  
  
I will need more information in order to figure it out.

---

## Comment 4

> Username: qwertzui11  
> Created at: 2017-05-24 17:33:34 UTC  
> Url: https://github.com/boostorg/beast/issues/386#issuecomment-303796066  

the error occurs on compiling the line  
`beast::http::read(...)`  
  
I set up a quick project  
https://github.com/qwertzui11/beast_issue386  
Because I'm not at work the compiler now is g++5.4.0, Boost-1.58 and ubuntu 16.04  
At work the same error occurs with g++6.1, Boost-1.64 and ubuntu 17.04  
Beast version is "master" (v44?)  
  
Another issue occured with g++5.4.0  
when I replace in https://github.com/qwertzui11/beast_issue386/blob/master/test.cpp line 1 and 2 with   
`#include <beast/core.hpp>` it doesnt compile because of the header files.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-05-24 19:40:53 UTC  
> Url: https://github.com/boostorg/beast/issues/386#issuecomment-303830072  

Yes! I was able to reproduce your defect. I believe this branch fixes your problem please try it:  
https://github.com/vinniefalco/Beast/commits/v44

---

## Comment 6

> Username: qwertzui11  
> Created at: 2017-05-24 20:26:28 UTC  
> Url: https://github.com/boostorg/beast/issues/386#issuecomment-303841617  

I tried and succeeded with both my compile issues. Only tested g++5.4, but I'm confident, g++6.1 won't have any issues.  
Thx a lot!

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-05-24 20:32:38 UTC  
> Url: https://github.com/boostorg/beast/issues/386#issuecomment-303843158  

Thank you for finding this bug!

---

## Comment 8

> Username: qwertzui11  
> Created at: 2017-06-12 10:40:13 UTC  
> Url: https://github.com/boostorg/beast/issues/386#issuecomment-307753622  

It seems like it doesn't compile if the `buffers_adapter` is a rvalue. Code says more than a 100 words:  
https://gitlab.com/qwertzui11/beast_issue386/blob/79a49b7a536a3db7c5cd364ef4ad715493558ed9/test.cpp#L21  
build_log:  
https://gitlab.com/qwertzui11/beast_issue386/builds/18426413  
Have I got something wrong? thx for your time!

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-06-12 10:45:04 UTC  
> Updated at: 2017-06-12 10:45:27 UTC  
> Url: https://github.com/boostorg/beast/issues/386#issuecomment-307754668  

Hmm the first link doesn't work. Nor the second.

---

## Comment 10

> Username: qwertzui11  
> Created at: 2017-06-12 10:47:25 UTC  
> Updated at: 2017-06-12 10:47:33 UTC  
> Url: https://github.com/boostorg/beast/issues/386#issuecomment-307755192  

it works now, sry, i failed in setting the premissions correctly

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-06-12 10:49:17 UTC  
> Url: https://github.com/boostorg/beast/issues/386#issuecomment-307755603  

```  
beast::http::read(  
    stream,  
    beast::buffers_adapter<cache_type::mutable_buffers_type>(streambuf.prepare(512)),  
    parser);  
```  
  
You're trying to bind a temporary to a non-const reference parameter? tsk tsk! I think that's not valid C++!

---

## Comment 12

> Username: qwertzui11  
> Created at: 2017-06-12 10:50:17 UTC  
> Updated at: 2017-06-12 10:50:23 UTC  
> Url: https://github.com/boostorg/beast/issues/386#issuecomment-307755837  

> tsk tsk!  
  
Ur right, lol, fail.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-06-12 10:50:36 UTC  
> Url: https://github.com/boostorg/beast/issues/386#issuecomment-307755912  

I wish I could say that I have never tried to do such a thing...

---

## Comment 14

> Username: vinniefalco  
> Created at: 2017-06-12 10:51:25 UTC  
> Url: https://github.com/boostorg/beast/issues/386#issuecomment-307756086  

That is a fascinating use-case though. What are you trying to accomplish? You will limit the maximum size of the header that way, to 512 bytes.

---

## Comment 15

> Username: qwertzui11  
> Created at: 2017-06-12 10:55:56 UTC  
> Url: https://github.com/boostorg/beast/issues/386#issuecomment-307757013  

I'm just playing around with asio::streambuf and beast - `512` is a random number.
