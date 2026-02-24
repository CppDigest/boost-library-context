# #546 - performance issue with transfer_encoding=chunked [Closed]

> Username: octopus-prime  
> Created at: 2017-06-26 21:47:34 UTC  
> Updated at: 2017-06-28 21:13:58 UTC  
> Closed at: 2017-06-28 21:13:58 UTC  
> Url: https://github.com/boostorg/beast/issues/546  

Using `set(transfer_encoding, "chunked")` is 2x slower than using` prepare_payload()`.  
Is this behavior normal?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-26 21:49:43 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311192173  

It depends on your payload. Beast will automatically set the chunked encoding if the body indicates that the size is not known. When using chunked encoding, the implementation has to make multiple calls to write to the socket. For example Beast might send the header and first chunk in one call to write, and then send the final chunk in another call. Maybe I can consolidate those calls into one? Can you provide example code, such as the code which sets up the message object?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-26 21:54:33 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311193179  

I could include a special case in the `serializer` when the first call into the body returns the last buffer but in that case you could have set Content-Length so I'm not sure I want to make that change.

---

## Comment 3

> Username: octopus-prime  
> Created at: 2017-06-26 22:35:32 UTC  
> Updated at: 2017-06-26 22:54:26 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311201152  

Sample code:  
  
```  
#include <beast/core.hpp>  
#include <beast/http.hpp>  
#include <boost/asio.hpp>  
#include <iostream>  
#include <string>  
#include <chrono>  
  
using namespace std::literals;  
  
int main()  
{  
    try  
    {  
        // Normal boost::asio setup  
        std::string const host = "hessian.caucho.com";  
        boost::asio::io_service ios;  
        boost::asio::ip::tcp::resolver resolver{ios};  
        boost::asio::ip::tcp::socket socket{ios};  
        boost::asio::ip::tcp::resolver::query query{host, "http"};  
        boost::asio::connect(socket, resolver.resolve(query));  
        beast::flat_buffer buffer;  
  
        auto const fast = [](beast::http::request<beast::http::string_body>& request)  
        {  
            request.prepare_payload();  
        };  
  
        auto const slow = [](beast::http::request<beast::http::string_body>& request)  
        {  
            request.set(beast::http::field::transfer_encoding, "chunked");  
        };  
  
        auto const test = [&](auto const prepare)  
        {  
            // Send HTTP request using beast  
            beast::http::request<beast::http::string_body> request;  
            request.method(beast::http::verb::post);  
            request.target("/test/test2");  
            request.set(beast::http::field::host, host);  
            request.set(beast::http::field::user_agent, "libhessian/1.0");  
            request.set(beast::http::field::content_type, "x-application/hessian");  
            request.body = "H\x02\x00""C\x09""replyTrue\x90"s;  
            prepare(request);  
//            std::clog << request << std::endl;  
            beast::http::write(socket, request);  
  
            // Receive and print HTTP response using beast  
            beast::http::response<beast::http::string_body> response;  
            beast::http::read(socket, buffer, response);  
//            std::clog << response << std::endl;  
        };  
  
        auto const run = [&](auto const str, auto const prepare)  
        {  
            auto const t0 = std::chrono::high_resolution_clock::now();  
            for (size_t i = 0; i < 50; ++i)  
                test(prepare);  
            auto const t1 = std::chrono::high_resolution_clock::now();  
            auto const t = std::chrono::duration_cast<std::chrono::milliseconds>(t1 - t0);  
            std::cout << "t[" << str << "] = " << t.count() << " ms" << std::endl;;  
        };  
  
        run("fast", fast);  
        run("slow", slow);  
    }  
    catch (std::exception const& exception)  
    {  
        std::cerr << "error: " << exception.what() << std::endl;  
    }  
    return 0;  
}  
```

---

## Comment 4

> Username: octopus-prime  
> Created at: 2017-06-26 22:37:45 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311201548  

```  
t[fast] = 5193 ms  
t[slow] = 12203 ms  
```

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-26 23:07:42 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311206617  

If you have a `string_body` why are you using chunked? You know the size.

---

## Comment 6

> Username: octopus-prime  
> Created at: 2017-06-27 00:54:55 UTC  
> Updated at: 2017-06-27 00:56:31 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311222123  

Currently thats for performance tests only.  
But imagine stream bodies.  
To provide the most flexible interface in a http-client, e.g.  
```  
void get(beast::string_view target, std::ostream& output);  
void put(beast::string_view target, std::istream& input);  
void post(beast::string_view target, std::istream& input, std::ostream& output);  
```  
Then you would use chunked encoding.  
I wanted to know the impact.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-06-27 01:02:02 UTC  
> Updated at: 2017-06-27 01:03:58 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311223136  

With a stream body, you don't know the size ahead of time, so you will pay one I/O for each buffer you get back from the stream, plus one I/O for the "final chunk." If you know ahead of time that the buffer is the "last" buffer then the final chunk is free.  
  
The only case where you pay for one extra unnecessary I/O is the case where the stream produces only one buffer, and you know its the last buffer. In this case you pay for the header+body chunk (one write) and then you pay for one final-chunk. Is this important to be addressed?  
  
I'll look into it.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-06-27 01:05:23 UTC  
> Updated at: 2017-06-27 01:05:45 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311223611  

Beast tries very hard to do as much as possible in each I/O (for the case of serializing, writes). The serializer combines the header with the body when possible:  
https://github.com/vinniefalco/Beast/blob/8982e14aa65b9922ac5a00e5a5196a08dfa8f29e/include/beast/http/serializer.hpp#L160

---

## Comment 9

> Username: octopus-prime  
> Created at: 2017-06-27 01:09:52 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311224270  

> Is this important to be addressed?  
  
Not yet - i think. But it might in future.  
May be other users want to use streams too.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-06-27 01:12:18 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311224630  

>May be other users want to use streams too.  
  
Nothing stops you from using streams with the current implementation. Anyway, I'm going to write the code for this so hang on.

---

## Comment 11

> Username: octopus-prime  
> Created at: 2017-06-27 01:17:09 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311225411  

The debug-output shows  
```  
POST /test/test2 HTTP/1.1  
Host: hessian.caucho.com  
User-Agent: libhessian/1.0  
Content-Type: x-application/hessian  
Content-Length: 15  
  
HC	replyTrue�  
  
```  
and  
```  
POST /test/test2 HTTP/1.1  
Host: hessian.caucho.com  
User-Agent: libhessian/1.0  
Content-Type: x-application/hessian  
Transfer-Encoding: chunked  
  
f  
HC	replyTrue�  
0  
  
  
  
```  
So the 2nd I/O is for this "0\n\n"?

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-06-27 01:18:07 UTC  
> Updated at: 2017-06-27 01:19:45 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311225540  

>So the 2nd I/O is for this "0\n\n"?  
  
Yes. Its actually `"0\r\n\r\n"`  
  
It comes from here:  
  
https://github.com/vinniefalco/Beast/blob/8982e14aa65b9922ac5a00e5a5196a08dfa8f29e/include/beast/http/serializer.hpp#L183  
  
and  
  
https://github.com/vinniefalco/Beast/blob/8982e14aa65b9922ac5a00e5a5196a08dfa8f29e/include/beast/http/impl/serializer.ipp#L225  
  
`detail::chunk_final` is `"0\r\n"` while `detail::chunk_crlf()` is just `"\r\n"`  
  
Note that there can be "trailers" if you set a ChunkDecorator

---

## Comment 13

> Username: octopus-prime  
> Created at: 2017-06-27 01:21:02 UTC  
> Updated at: 2017-06-27 01:21:31 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311225917  

Ignoring the "trailers" for the moment...  
Somebody could append "0\n\n" in the 1st (or last in general) I/O if he knows the stream is done?!

---

## Comment 14

> Username: vinniefalco  
> Created at: 2017-06-27 01:23:09 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311226202  

In theory yes but in practice no, because how would you know if the serializer was going to do it or not? You could end up with double the final chunks. Also, its `"0\r\n\r\n"`. There are two CRLF sequences, when no trailers are present.

---

## Comment 15

> Username: octopus-prime  
> Created at: 2017-06-27 01:26:31 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311226624  

It was just a naive question :D

---

## Comment 16

> Username: octopus-prime  
> Created at: 2017-06-27 01:32:29 UTC  
> Updated at: 2017-06-27 01:33:38 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311227401  

```  
t[fast] = 5195 ms  
t[slow] = 5194 ms  
```  
  
wow!!

---

## Comment 17

> Username: vinniefalco  
> Created at: 2017-06-27 01:34:18 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311227614  

Sometimes the good guys win

---

## Comment 18

> Username: octopus-prime  
> Created at: 2017-06-27 01:35:18 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311227754  

That's much more performance boost than you can get by string_views etc...

---

## Comment 19

> Username: vinniefalco  
> Created at: 2017-06-27 01:45:08 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311229061  

Yeah, remember though that it doesn't scale, you're only saving one I/O for all the chunks. So if you have 100 chunks you're only saving one I/O. With 1,000 chunks, save one I/O....  
  
It just seems like a lot because if you have 1 chunk it was doing 2 I/Os and when you save 1 on a 2 I/O operation you are effectively "doubling" the speed but that's not going to keep happening obviously.

---

## Comment 20

> Username: vinniefalco  
> Created at: 2017-06-27 02:25:54 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311234718  

Don't forget to participate in the Boost formal review!!

---

## Comment 21

> Username: octopus-prime  
> Created at: 2017-06-27 06:09:58 UTC  
> Updated at: 2017-06-28 06:39:08 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311262529  

For RPC calls the body is often very small. So this is a nice speedup :-)  
How can i participate in the Boost formal review?

---

## Comment 22

> Username: vinniefalco  
> Created at: 2017-06-27 10:52:34 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311323549  

Participating in the review is easy, just sign up to the "Boost developers mailing list" here:  
http://www.boost.org/community/groups.html#main  
  
And then wait for the announcement. The review starts on July 1st. They will be very interested to hear from people that have used Beast. You can write as little or as much as you want for the review. And if you have something bad to say about Beast, that's okay too - feedback is what helps improve the library! There's a bunch of smart folks on the mailing list and you seem pretty intelligent so I think you will enjoy participating! I always learn a ton of stuff when libraries get reviewed.

---

## Comment 23

> Username: vinniefalco  
> Created at: 2017-06-27 11:53:50 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311335652  

valgrind doesn't like the change:  
https://travis-ci.org/vinniefalco/Beast/builds/247373497

---

## Comment 24

> Username: vinniefalco  
> Created at: 2017-06-27 15:23:20 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311393195  

This change https://github.com/vinniefalco/Beast/commit/868beb2ee7af04f192175d97a9f34a1b6ae09bd9  
  
Blows up Travis: https://travis-ci.org/vinniefalco/Beast/jobs/247553508

---

## Comment 25

> Username: octopus-prime  
> Created at: 2017-06-27 17:23:31 UTC  
> Updated at: 2017-06-27 17:24:49 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311427055  

Looking at  
https://travis-ci.org/vinniefalco/Beast/jobs/247553508  
i saw this:  
  
`  
error: template instantiation depth exceeds maximum of 128 (use -ftemplate-depth= to increase the maximum) instantiating  
`  
  
Yes, with lot of template voodoo the maximum of 128 is too low. I always build with  
`-ftemplate-depth=256`  
at least.  
  
May be all other errors are produced directly/indirectly by the error above.  
  
Don't know why gcc has this limit... clang has not. msvc has not - i think...

---

## Comment 26

> Username: vinniefalco  
> Created at: 2017-06-27 17:25:03 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311427504  

Using Boost 1.64.0 also resolves the issue

---

## Comment 27

> Username: vinniefalco  
> Created at: 2017-06-27 17:26:20 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311427894  

I'm going to enable this optimization only when using Boost 1.64.0 and higher. I will leave Travis on Boost 1.58.0. Locally I develop with 1.64.0. When Beast is merged into Boost, then the optimization will be always-on.

---

## Comment 28

> Username: octopus-prime  
> Created at: 2017-06-27 17:30:57 UTC  
> Updated at: 2017-06-27 17:31:17 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311429139  

I think with boost 1.64 you are still hard before the limit. Let's say 127...  
So it is a question of time when the error will come back.  
I would set `-ftemplate-depth=256` for gcc.  
In my opinion this has no impact to build process.

---

## Comment 29

> Username: vinniefalco  
> Created at: 2017-06-27 17:33:08 UTC  
> Updated at: 2017-06-27 17:33:23 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311429729  

I'm using this logic in **v69** now  
```  
#ifndef BEAST_NO_BIG_VARIANTS  
# if BOOST_WORKAROUND(BOOST_GCC, < 50000) || BOOST_VERSION < 106400  
#  define BEAST_NO_BIG_VARIANTS 1  
# else  
#  define BEAST_NO_BIG_VARIANTS 0  
# endif  
#endif  
```  
  
I rather not fiddle with the default template depth if I can avoid it. Its not just the template depth but a unit test hangs, which suggests incorrect code generation with gcc versions below 5. Please try the branch and let me know that it works for you (including the optimization, which should be ON for your configuration).

---

## Comment 30

> Username: octopus-prime  
> Created at: 2017-06-27 18:14:19 UTC  
> Updated at: 2017-06-27 18:20:15 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311440994  

BEAST_NO_BIG_VARIANTS: 1  
t[fast] = 5414 ms  
t[slow] = 12600 ms

---

## Comment 31

> Username: vinniefalco  
> Created at: 2017-06-27 18:22:34 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311443149  

Are you saying `BEAST_NO_BIG_VARIANTS` is `1` on your setup? Is it because you are on gcc 4.x or because you are on an earlier Boost?

---

## Comment 32

> Username: octopus-prime  
> Created at: 2017-06-27 18:23:19 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311443341  

BEAST_NO_BIG_VARIANTS: 1  
is output at runtime.

---

## Comment 33

> Username: octopus-prime  
> Created at: 2017-06-27 18:24:05 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311443546  

gcc is 7.0.1  
boost is 1.63

---

## Comment 34

> Username: octopus-prime  
> Created at: 2017-06-27 18:24:59 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311443789  

Ah. boost 1.63 and BOOST_VERSION < 106400 ...

---

## Comment 35

> Username: octopus-prime  
> Created at: 2017-06-27 18:25:46 UTC  
> Updated at: 2017-06-27 18:32:00 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311443977  

This is better  
```  
# if BOOST_WORKAROUND(BOOST_GCC, < 50000) && BOOST_VERSION < 106400  
```  
isn't it?!  
  
Only gcc 4.8 with older boost is problematic...

---

## Comment 36

> Username: vinniefalco  
> Created at: 2017-06-27 18:36:08 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311446818  

I'll update it

---

## Comment 37

> Username: vinniefalco  
> Created at: 2017-06-27 20:04:57 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311469529  

This is still creating problems on Travis:  
https://travis-ci.org/vinniefalco/Beast/builds/247644770  
  
I have to remove it from v69 and investigate.

---

## Comment 38

> Username: octopus-prime  
> Created at: 2017-06-27 22:04:32 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311499555  

So what's the problem here? The lcov errors?

---

## Comment 39

> Username: vinniefalco  
> Created at: 2017-06-27 22:09:45 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311500583  

Yes. No idea what causes it. They have happened before. If you note, that version doesn't even compile in the optimizations (I think).

---

## Comment 40

> Username: octopus-prime  
> Created at: 2017-06-28 06:15:14 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311566532  

Meanwhile you committed 2 changes.  
How can i see the build results for these commits?

---

## Comment 41

> Username: vinniefalco  
> Created at: 2017-06-28 10:38:46 UTC  
> Updated at: 2017-06-28 10:42:56 UTC  
> Url: https://github.com/boostorg/beast/issues/546#issuecomment-311623012  

https://travis-ci.org/vinniefalco/Beast/builds
