# #430 - bug when reading chunked on "slow" stream [Closed]

> Username: qwertzui11  
> Created at: 2017-06-07 12:26:57 UTC  
> Updated at: 2017-06-08 15:23:34 UTC  
> Closed at: 2017-06-08 03:14:43 UTC  
> Url: https://github.com/boostorg/beast/issues/430  

### Version of Beast  
master (v49) and v50  
  
### compiler  
os: ubuntu 17.04  
compiler: gcc (Ubuntu 6.3.0-12ubuntu2) 6.3.0 20170406  
  
### code for repeating the error  
```  
struct very_slow_stream {  
  boost::asio::io_service& io_service;  
  const std::string input =  
      "HTTP/1.1 200 OK\r\n"  
      "Transfer-Encoding: chunked\r\n"  
      "Content-Type: application/octet-stream\r\n"  
      "\r\n"  
      "4\r\n"  
      "abcd\r\n"  
      "0\r\n"  
      "\r\n";  
  std::size_t index{};  
  
  template <typename MutableBufferSequence>  
  std::size_t read_some(const MutableBufferSequence& buffer,  
                        boost::system::error_code& /*error*/) {  
    auto write_to = boost::asio::buffer_cast<char*>(*buffer.begin());  
    // std::size_t copy_count{input.size()}; // WORKS!  
    std::size_t copy_count{1};  // FAILS, with bad chunk!  
    for (std::size_t index_copy = 0; index_copy < copy_count; ++index_copy)  
      *(write_to + index_copy) = input[index_copy + index];  
    index += copy_count;  
    return copy_count;  
  }  
  
  template <typename MutableBufferSequence>  
  std::size_t read_some(const MutableBufferSequence& buffer) {  
    boost::system::error_code error;  
    const auto size = read_some(buffer, error);  
    if (error) throw error;  
    return size;  
  }  
  boost::asio::io_service& get_io_service() { return io_service; }  
};  
  
void test_bug() {  
  boost::asio::io_service service;  
  very_slow_stream stream_{service};  
  beast::multi_buffer stream_buffer;  
  beast::http::parser<false, beast::http::dynamic_body, beast::http::fields>  
      parser;  
  
  while (!parser.is_done()) {  
    const auto read_count =  
        beast::http::read_some(stream_, stream_buffer, parser);  
    std::cout << parser.get() << std::endl;  
    stream_buffer.consume(read_count);  
  }  
  std::cout << beast::buffers(parser.get().body.data()) << std::endl;  
}  
  
```  
  
### the issue  
I'm trying to read a chunked http stream, !chunk by chunk!. So I'm using `beast::http::read_some`.  
!Sometimes!, I get the "bad chunk" error when beast tries to parse the chunk header (`pasic_parser.ipp:546`). After some hours of debugging, I found out the error occurs when the stream happens to be very slow. There seems to be a logic error with the `\r\n` of the previous body chunk.

---

## Comment 1

> Username: qwertzui11  
> Created at: 2017-06-07 12:29:21 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-306779713  

Am I doing something terrible wrong, or did I find an issue?  
Thx for your time!

---

## Comment 2

> Username: qwertzui11  
> Created at: 2017-06-07 12:43:48 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-306782978  

```beast::http::read(stream_, stream_buffer, parser);```  
fails too, if the stream is too slow.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-07 13:23:30 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-306793147  

Great minds think alike! It looks like you have reinvented a couple of my test classes:  
https://github.com/vinniefalco/Beast/blob/master/extras/beast/test/pipe_stream.hpp#L30  
https://github.com/vinniefalco/Beast/blob/master/extras/beast/test/string_istream.hpp#L23  
  
I'm a little bit, but not completely, surprised that you found a bug. I have changed that code recently and fixed a few bugs in it. There is probably one more bug remaining. I thought I had tests that explicitly exercise a matrix of input where there is a buffer break at every possible location, but I can't seem to find it now.  
  
You seem pretty smart, so while I am tracking this down if you want to maybe take a look at the implementation, perhaps you will spot the bug right away:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/impl/basic_parser.ipp#L487  
  
Lines 513-522 in particular...

---

## Comment 4

> Username: qwertzui11  
> Created at: 2017-06-07 13:42:16 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-306798355  

I'm on it. :+1: :smiley:

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-07 13:58:05 UTC  
> Updated at: 2017-06-07 13:58:15 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-306802994  

Not "the" bug, but a bug none the less: (**v50**) https://github.com/vinniefalco/Beast/commit/a33ca12b3deb8ffc79a8bd531e21b4dbcb0b5df6

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-06-07 14:08:39 UTC  
> Updated at: 2017-06-07 14:08:58 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-306806149  

*massive* bug in the test pipe, it uses write_size instead of read_size! That means none of my test code that thought it was reading a byte at a time was doing what it think it do: https://github.com/vinniefalco/Beast/commit/e4a4725838dd827b3af09b28cc2eaf0d173e8e20  
  
I can reproduce your issue with this test function:  
```  
// https://github.com/vinniefalco/Beast/issues/430  
void  
testRegression430()  
{  
    test::pipe c{ios_};  
    c.server.read_size(1);  
    ostream(c.server.buffer) <<  
        "HTTP/1.1 200 OK\r\n"  
        "Transfer-Encoding: chunked\r\n"  
        "Content-Type: application/octet-stream\r\n"  
        "\r\n"  
        "4\r\nabcd\r\n"  
        "0\r\n\r\n";  
    flat_buffer fb;  
    parser<false, dynamic_body> p;  
    read(c.server, fb, p);  
}  
```

---

## Comment 7

> Username: qwertzui11  
> Created at: 2017-06-07 14:15:49 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-306808348  

great to read!  
I believe the issue is the removal of the flag `flagExpectCRLF`  
https://github.com/vinniefalco/Beast/blob/v50/include/beast/http/impl/basic_parser.ipp#L525  
later the method exits at   
https://github.com/vinniefalco/Beast/blob/v50/include/beast/http/impl/basic_parser.ipp#L536  
and gets called again without skipping \r\n

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-06-07 14:17:47 UTC  
> Updated at: 2017-06-07 14:18:26 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-306808924  

`basic_parser::put` states (https://github.com/vinniefalco/Beast/blob/ac5bc4f62c318812104ad2cef13b2d3a19ada8bb/include/beast/http/basic_parser.hpp#L387):  
>The caller should remove these octets even if the error is set.  
  
But my own code doesn't follow the rules!  
https://github.com/vinniefalco/Beast/blob/ac5bc4f62c318812104ad2cef13b2d3a19ada8bb/include/beast/http/impl/read.ipp#L472  
  
`read_some` reads the `\r\n` and since the parser returned "need more" it loops again but it didn't consume the bytes.  And it can't consume them because the contract of `read_some` doesn't allow it.  
  
This will require some thinking for a robust fix, its not straightforward...

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-06-07 14:18:36 UTC  
> Updated at: 2017-06-07 14:19:03 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-306809172  

Hey I think you're right about removing the flag! But keeping it is not so easy because of the transition from a regular chunk to the final chunk...working on a fix.

---

## Comment 10

> Username: bitbugprime  
> Created at: 2017-06-07 15:29:33 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-306831521  

I'm also seeing errors reading chunks. Some debug output. You can see the parser is getting confused about where the next chunk size is located, so it attempts to parse ":" as a hex value and naturally fails. Also note the suspiciously ethernet frame sized last chunk on the failure.  
  
chunk size 1f44  
start of chunk: {"results":[{"type":"data","name":"foo_076491001","locations":[{"cluster_name":"char  
parse_chunk_header n = 286 skip_ = 0  
parse_chunk_header hex value is 1f44 8004  
parse_chunk_header state::chunk_body  
parse_chunk_body n = 280  
parse_chunk_body len_ > 0 = 7724  
parse_chunk_body n = 1024  
parse_chunk_body len_ > 0 = 6700  
parse_chunk_body n = 1024  
parse_chunk_body len_ > 0 = 5676  
parse_chunk_body n = 1024  
parse_chunk_body len_ > 0 = 4652  
parse_chunk_body n = 1024  
parse_chunk_body len_ > 0 = 3628  
parse_chunk_body n = 1024  
parse_chunk_body len_ > 0 = 2604  
parse_chunk_body n = 1024  
parse_chunk_body len_ > 0 = 1580  
parse_chunk_body n = 1024  
parse_chunk_body len_ > 0 = 556  
parse_chunk_body n = 556  
parse_chunk_body state::chunk_header  
parse_chunk_header expect crlf  
parse_chunk_header clear crlf  
chunk size 2000  
start of chunk: me":"vhost","hostname":"vhost","port":9005},{"cluster_name":"cluster  
parse_chunk_header n = 466 skip_ = 0  
parse_chunk_header hex value is 2000 8192  
parse_chunk_header state::chunk_body  
parse_chunk_body n = 460  
parse_chunk_body len_ > 0 = 7732  
parse_chunk_body n = 1024  
parse_chunk_body len_ > 0 = 6708  
parse_chunk_body n = 1024  
parse_chunk_body len_ > 0 = 5684  
parse_chunk_body n = 1024  
parse_chunk_body len_ > 0 = 4660  
parse_chunk_body n = 566  
parse_chunk_body len_ > 0 = 4094  
parse_chunk_body n = 1024  
parse_chunk_body len_ > 0 = 3070  
parse_chunk_body n = 1024  
parse_chunk_body len_ > 0 = 2046  
parse_chunk_body n = 1024  
parse_chunk_body len_ > 0 = 1022  
parse_chunk_body n = 1022  
parse_chunk_body state::chunk_header  
parse_chunk_header expect crlf  
parse_chunk_header clear crlf  
parse_chunk_header n = 0 skip_ = 0  
parse_chunk_header n < skip_ + 2  
chunk size: ":"hostname","port":9005},{"cluster_name":"charliey","hostname":"d2charlieydbsrvp  
parse_chunk_header n = 1536 skip_ = 0  
parse_hex first unhex failed  
parse_chunk_header parse_hex failed  
std::exception_ptr bad chunk

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-06-07 15:38:37 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-306834336  

I've identified a defect in the parsing of chunk headers, working on a fix

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-06-07 17:51:33 UTC  
> Updated at: 2017-06-07 17:54:27 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-306873355  

This fix passes tests (including the new one that fails) https://github.com/vinniefalco/Beast/commit/c93eac05b7edfef2c35f714df86cacc1f3f92cb1  
  
Its in the **v50** branch.  
  
@qwertzui11 @randian Thanks for all the help. But I would humbly ask for a little bit more - I am in great need of assistance! Specifically, I need feedback on all of the recent changes. There have been a lot of them and most of them API-breaking (sorry for that but its in the name of progress). I need to know if I am heading in the wrong direction - so please do not be shy or worry about hurting my feelings. If you have something critical, I need to know sooner rather than later!  
  
I would also suggest looking over this parsing change (commit above) to see how you feel about it.  
  
Feel free to open issues on any subject. Interface. documentation, implementation, things you'd like to see, what you think is bad, what could be improved, what you like, i.e. what you think is good (I always like hearing that).

---

## Comment 13

> Username: bitbugprime  
> Created at: 2017-06-07 20:44:51 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-306919158  

One thing is the documentation lags way behind the API changes, so figuring out how existing code has to change can be frustrating. A quick note saying "this changed, so do this now instead" would be nice.  
  
For example, v50 appears to have changed  
  
beast::http::prepare(message, beast::http::connection::close);  
  
to  
  
message.prepare().  
  
But prepare no longer takes an options argument and in any case beast::http::connection seems to have disappeared too.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2017-06-07 20:48:48 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-306920239  

@randian Yep, sorry about that. There's a June 17th "readiness" deadline and July 1st hard stop so I am trying to cram as much API change as possible into the library before it is frozen in review. You're right that the documentation lags behind. I've been working really hard, I hope that the results are worth some of this temporary pain.  
  
There's no more interface to set keep-alive or close on the connection, this was necessary to reduce the **Fields** down to the minimum.

---

## Comment 15

> Username: bitbugprime  
> Created at: 2017-06-07 21:12:01 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-306926526  

v50 still doesn't work for me.  
  
parse_chunk_header not final chunk  
parse_chunk_header hex value is 1f44 8004  
parse_chunk_header state::chunk_body  
parse_chunk_body n = 280  
parse_chunk_body len_[7724] > 0  
parse_chunk_body n = 1024  
parse_chunk_body len_[6700] > 0  
parse_chunk_body n = 1024  
parse_chunk_body len_[5676] > 0  
parse_chunk_body n = 1024  
parse_chunk_body len_[4652] > 0  
parse_chunk_body n = 1024  
parse_chunk_body len_[3628] > 0  
parse_chunk_body n = 1024  
parse_chunk_body len_[2604] > 0  
parse_chunk_body n = 1024  
parse_chunk_body len_[1580] > 0  
parse_chunk_body n = 1024  
parse_chunk_body len_[556] > 0  
parse_chunk_body n = 556  
parse_chunk_body state::chunk_header  
parse_chunk_header not final chunk  
parse_chunk_header expect crlf  
parse_chunk_header hex value is 2000 8192  
parse_chunk_header state::chunk_body  
parse_chunk_body n = 460  
parse_chunk_body len_[7732] > 0  
parse_chunk_body n = 1024  
parse_chunk_body len_[6708] > 0  
parse_chunk_body n = 1024  
parse_chunk_body len_[5684] > 0  
parse_chunk_body n = 1024  
parse_chunk_body len_[4660] > 0  
parse_chunk_body n = 566  
parse_chunk_body len_[4094] > 0  
parse_chunk_body n = 1024  
parse_chunk_body len_[3070] > 0  
parse_chunk_body n = 1024  
parse_chunk_body len_[2046] > 0  
parse_chunk_body n = 1024  
parse_chunk_body len_[1022] > 0  
parse_chunk_body n = 1022  
parse_chunk_body state::chunk_header  
parse_chunk_header not final chunk  
parse_chunk_header n[2] < skip_ + 2[4]  
parse_chunk_header not final chunk  
parse_chunk_header expect crlf  
parse_chunk_header parse_crlf failed  
std::exception_ptr bad chunk

---

## Comment 16

> Username: vinniefalco  
> Created at: 2017-06-07 21:15:15 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-306927259  

@randian Any way to reduce that down to a reproducible test case, for example in one of the unit test .cpp files in Beast?

---

## Comment 17

> Username: vinniefalco  
> Created at: 2017-06-07 21:37:00 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-306932600  

I added a much stronger test and found a defect, working on a fix...

---

## Comment 18

> Username: bitbugprime  
> Created at: 2017-06-07 21:47:28 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-306934994  

Cool. I'll wait to see if your new fix solves my problem.

---

## Comment 19

> Username: vinniefalco  
> Created at: 2017-06-07 23:27:12 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-306954330  

I think https://github.com/vinniefalco/Beast/commit/498600dbd02df6b6197b596f3d198ca029f13586 could solve your problem, I rewrote the **v50** branch.

---

## Comment 20

> Username: qwertzui11  
> Created at: 2017-06-08 07:04:42 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-307017874  

I was able to retest and everything works as expected (master v50), thank you!

---

## Comment 21

> Username: vinniefalco  
> Created at: 2017-06-08 07:05:37 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-307018021  

Phew, that's a relief!!! Thanks!

---

## Comment 22

> Username: bitbugprime  
> Created at: 2017-06-08 15:23:34 UTC  
> Url: https://github.com/boostorg/beast/issues/430#issuecomment-307137540  

Master v50 is working for me too. Thanks!
