# #1892 - POST question of sending/processing of file_body type [Closed]

> Username: jade2k11598  
> Created at: 2020-04-06 17:54:27 UTC  
> Updated at: 2020-05-04 11:13:27 UTC  
> Closed at: 2020-05-04 11:13:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1892  

Hi @vinniefalco,  
I'd like to inquire more on the [statements made here](https://github.com/boostorg/beast/issues/1304#issuecomment-439406151).  
  
You noted that   
  
> It reads the file one 4KB piece at a time and sends each piece  
  
So when one calls the `http::async_write` with a POST request, in that one call to `async_write`, once the header is sent, because the body is `http::body_file`,  it will **continuously reads** the file, 4KB at a time, sending each piece through the socket, until it comes to the end of the file?  
  
If so, how should the receiving end manage this, particularly if it is a large file being transmitted?  Can it also create an `http::body_file` on the other end and have **what it reads as the body, write directly to file,** so it doesn't need to buffer a large chunk of memory to process the receiving of that body that just gets written to file?  
  
I'm thinking along the lines of sending a POST request with `expect: 100-continue`.  Do I create `http::request_parser<http::file_body>`?  Something like this:  
  
```  
    beast::error_code ec;  
    http::file_body::value_type body;  
    body.open(path.c_str(), beast::file_mode::append, ec);  
...  
   request_parser<http::file_body> parser{  
            std::piecewise_construct,  
            std::make_tuple(std::move(body))};  
...  
    // Read the header  
    read_header(stream, buffer, parser, ec);  
    if(ec)  
        return;  
  
    // Check for the Expect field value  
    if(parser.get()[field::expect] == "100-continue")  
    {  
        // send 100 response  
        response<empty_body> res;  
        res.version(11);  
        res.result(status::continue_);  
        res.set(field::server, "test");  
        write(stream, res, ec);  
        if(ec)  
            return;  
    }  
  
    // Read the rest of the message which should be the body  
    read(stream, buffer, parser, ec);  
```  
Such that that last read just continuously reads the series of 4KB buffer and writing it directly to file, until it comes to the end of what's in the socket (e.g. don't need to call `read` repeatedly for each 4KB sent)?  Granted the above code is a synchronous read but I imagine the async version is similar.  
  
As a side question.  I notice this `beast::flat_buffer buffer` passed into the `read`, despite already having passed it a parser/request object; what exactly is the purpose of this buffer?  I notice that after each read issued, sampling this `buffer` it seems always empty.  So I don't quite understand its purpose.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-04-06 17:58:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1892#issuecomment-609946959  

> what exactly is the purpose of this buffer  
  
https://www.boost.org/doc/libs/1_72_0/libs/beast/doc/html/beast/using_http/message_stream_operations.html#beast.using_http.message_stream_operations.reading

---

## Comment 2

> Username: jade2k11598  
> Created at: 2020-04-06 18:20:30 UTC  
> Updated at: 2020-04-06 18:20:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1892#issuecomment-609957916  

Thanks for that reference on the purpose of the `beast::flat_buffer buffer` passed to read.    
  
But on that main question ... is my assumptions correct on the sending end for `file_body` type, that a one write call will continuously read the file, sending it 4KB at a time until it comes to the end of the file?   
 And how it can be efficiently read off socket and into file on the receiving end by also creating a `file_body` on the receiving end?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-04-06 18:22:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1892#issuecomment-609959153  

yes `http::write` and `http::async_write` will send the whole file.  
  
If you want to read the entire file on the other end then use `http::read` or `http::async_read`.

---

## Comment 4

> Username: jade2k11598  
> Created at: 2020-04-07 18:26:39 UTC  
> Updated at: 2020-04-07 18:44:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1892#issuecomment-610547996  

So I actually tested this and for those that are following along with the sample code I presented at the top, you don't actually know yet what file name is until after receiving the request since it'll be in the target name.  Only after confirming the format of the request (e.g. 100-continue), then open the body to the appropriate filename, using the target value sent.  
  
Now I got this working for small size files, but when I had a larger file size (e.g. 52Mbytes), when the server executed `http::async_read_header` (only first read the request header since it was a POST message with `expect: 100-continue`), it got an error of `body limit exceeded`.  I was a bit puzzled by this because at this point, it hasn't yet read the body.  
  
Now when I had prepared the request message on the client end, I did the following:  
```  
        beast::error_code ec;  
        req_.body().open(filename, beast::file_mode::scan, ec);  
        if (ec)  
        {  
            std::cout << "Goterror:" << ec.message() << std::endl;  
            throw std::runtime_error(ec.message());  
        }  
...  
        // this will fill everything else like content length  
        req_.prepare_payload();  
```  
So that last line will insert `Content-Length: 52931748` in the request, which is basically the size of the file that the client will eventually send once the request is accepted.  So out of curiosity, I temporarily commented out `req_.prepare_payload();` and therefore leaving out `Content-Length`.   When sending this request, the server doesn't get an error anymore when executing `http::async_read_header`.  So it looks like the error `body limit exceeded` was due to the check on the value of `Content-Length`.    
  
But in any case, the server continue on, accepting the `Post with 100-continue` and sent the response to the client.  When the client got a good Post response, it then when on to executed ` http::async_write` the rest of the body, which was a `http::file_body` reading from a large file, which then failed with `Broken pipe` and I suppose it failed because the file is too large??  The server end definitely didn't drop the connection because it was waiting on `http::async_read`, before returning with a good error_code but zero `bytes_transferred`.  So it was probably with good reasons why the Post request was initially rejected by the server when it saw that the `Content-Length` was too large.  
  
So it doesn't look like this idea would work for large files (using `http::file_body` on both the client and server end).  Originally I thought because the body was set to `http::file_body`, it would send 5KB of content at a time until eof, therefore not exceeding whatever is the limit to the socket.  But now I'm not sure how to get this working for a large file and was hoping to avoid having to read the file to some intermediate buffer before sending it.    
  
Further the `Content-Length` should be set as the file length (only temporarily comment it  in this exercise to see what would happen), yet won't get past that point either in the reading of the header because it immediately rejects it because its value was too large.  
  
Any suggestion @vinniefalco?  BTW I ran this with boost version 1.72.

---

## Comment 5

> Username: jade2k11598  
> Created at: 2020-04-07 21:43:09 UTC  
> Updated at: 2020-04-07 22:08:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1892#issuecomment-610635548  

OK, I have some updates.  Prior to calling `http::async_read_header` on the server end, i just added this `body_limit` call:  
```  
http::request_parser<http::file_body> par_;  
...  
        par_.body_limit((std::numeric_limits<std::uint64_t>::max()));  
        // Read a request header  
        http::async_read_header(stream_, buffer_, par_,  
            beast::bind_front_handler(  
                &session::on_read_hd,  
                shared_from_this()));  
```  
Setting that `body_limit` to some large number did it!  Not only is it able to read headers that has `Content-Length` set to values that are large, but at the client end, it is also be able to write large files to the socket (no more `Broken pipe` errors on the client end when attempting to send the file_body contents).  
  
So my question is ... what's the bad side effects of setting this `body_limit` to some large size?  It's not like it's allocating that size chunk of memory, correct?  As long as the parser has a body type of `http::body_file` on the server end, when executing `http::async_read` once, it's still continuously reading 5KB  of data from socket and writing it directly to file, _at a time_, until it it hits eof of the socket, correct?   
  
Just want to make sure that my server (as well as my client) is not allocating some large chunk of memory ... which I hoped using `file_body` (on both ends) would achieve me ...

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-04-08 12:18:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1892#issuecomment-610924720  

The only 'bad' side effect of setting the header_limit to u64max is that a malicious web server could fill your disk up.  
  
This setting is used to determine the maximum amount of data that will be received in the body of the response. It is essentially acting as a safety limit on the size to which your file can grow.

---

## Comment 7

> Username: jade2k11598  
> Created at: 2020-04-08 20:55:58 UTC  
> Updated at: 2020-04-08 22:13:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1892#issuecomment-611189977  

Hi,   
thanks for the response.  If you'll forgive me for asking one more question about this `http::file_body`.  I needed to anticipate that when transferring files, the client might lose connectivity and would like to take up the transferring of the file from where it left off.  To avoid having to restart the POST'ing of the whole file again, the client will trigger a HEAD/PATCH combination protocol.  So the HEAD request will query the server how much of the file was successfully transferred and if it wasn't the whole file, the HEAD response will tell me the off-set the client needs to start reading from the file.  
  
So in preparing for the PATCH request on the client end, I do something like this when setting up the file_body:  
```  
        // offset is offset received from HEADER response  
        ifs.seek(offset, ec);  
        if (ec)  
        {  
            std::cout << "Got seek error:" << ec.message() << std::endl;  
            throw std::runtime_error(ec.message());  
        }  
  
        req_.body().reset(std::move(ifs), ec);  
        if (ec)  
        {  
            std::cout << "Goterror:" << ec.message() << std::endl;  
            throw std::runtime_error(ec.message());  
        }  
        auto content_sz = req_.body().size() -  offset;  
        req_.set(http::field::content_length, std::to_string(content_sz));  
...  
        // Got 100-continue accepted, now send body file  
        http::async_write(stream_, req_,  
                beast::bind_front_handler(  
                &session::on_write_file,  
                shared_from_this()));  
```  
So at the setup of the PATCH request (with 100-continue), I first opened up the file and set the offset of where it should begin reading the file from.  And then reset the `file_body` with that instance of `beast::file`.  Once I got the OK from the server that it's ready to accept the body, then I send the body in that last `async_write`.  
  
Now the server receives the rest of the file fine and I see that the server does receive the full number of bytes it expected.  But the client seems to keep writing until the stream expiration (the `async_write` finishes with `ec=The socket was closed due to a timeout`).  It seems like even though the client came to the `eof`, it _attempted_ to continue to write to the socket until the expiration of the stream.     
  
My guess of what's happening here is, when the client attempts to read from a `file_body`, it's expecting to write to the socket the total file size (e.g. `req_.body().size() `).  But because I offset from where it should begin to read the file,  the number of bytes left to read from the file, is going to be less than the total size of the file, which causes the write to continue waiting (since it hasn't written the total # of bytes of the file), until the expiration.  
  
But shouldn't `http::async_write` just writing to the socket _until it comes to `eof`_ for a `body_file`, or at least to what the `content-length` indicate, which was set appropriately?   While the transfer of the file turned out OK, I'd rather that the `http::async_write` not return an error of timeout (and take longer than it should, when the full file already finished transferring), since it just confuses what's going on.  
  
I guess what I'm asking is, how can I make a partial transfer of a file with `http::file_body` work?  The hurdle that I"m facing is that even though I figured out how to position where to start reading from the file via `beast::file` and its `seek` function, I can't seem to control the size it should send (e.g. content-length), without causing an error with the `http::async_write`.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-04-09 05:20:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1892#issuecomment-611334023  

Typically when you don't trust a connection (i.e. the first time it connects, with no credentials) you set a low limit to prevent denial of service attacks. Or you may disallow uploads completely, until the client authenticates. Then you increase the body limit as necessary.

---

## Comment 9

> Username: jade2k11598  
> Created at: 2020-04-10 18:37:47 UTC  
> Updated at: 2020-04-10 18:38:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1892#issuecomment-612161964  

So on the issue that if one desires to have a `http::file_body` start reading elsewhere, other than the beginning of the file,  I figured out a way so that the expected body size is set correctly:  
```  
class MyFile: public beast::file  
{  
public:  
    std::uint64_t  
    size(beast::error_code& ec) const  
    {  
        auto currentPos = pos(ec);  
        if (ec)  
        {  
            return 0;  
        }  
        return beast::file::size(ec) - currentPos;  
    }  
};  
  
using MyFileBody = http::basic_file_body<MyFile>;  
...  
http::request<MyFileBody> req_;  
...  
  
        MyFile ifs;  
        ifs.open(filename.c_str(), beast::file_mode::scan, ec);  
        if (ec)  
        {  
            std::cout << "Failed to open " << filename << " with error:"  
                      << ec.message() << std::endl;  
            throw std::runtime_error(ec.message());  
        }  
        // set where to start reading from by offset  
        ifs.seek(offset, ec);  
        if (ec)  
        {  
            std::cout << "Got seek error:" << ec.message() << std::endl;  
            throw std::runtime_error(ec.message());  
        }  
        std::cout << "ifs after seek file size =" << ifs.size(ec) << std::endl;  
  
        req_.body().reset(std::move(ifs), ec);  
        if (ec)  
        {  
            std::cout << "Got body().reset error:" << ec.message() << std::endl;  
            throw std::runtime_error(ec.message());  
        }  
```  
Using `MyFileBody` instead of `http::file_body`, now appropriately sets the body size correctly (which is now less than the total file size since it's no longer reading from the beginning of the file).  But should this not be the correct behavior of `http::file_body`?  That is its `size()` function should take into account the current position of the pointer in the file, since function `pos(beast::error_code &)` dictates where it starts to read ...

---

## Comment 10

> Username: vinniefalco  
> Created at: 2020-04-26 16:24:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1892#issuecomment-619579585  

>  That is its size() function should take into account the current position of the pointer in the file  
  
No, `size()` is intended to always return the file size. I agree that the interface to file I/O needs work. However, you can always work around it by sending or receiving the file yourself, using `http::async_read_some` and using a `buffer_body`.

---

## Comment 11

> Username: madmongo1  
> Created at: 2020-05-04 11:13:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1892#issuecomment-623402693  

Hi @jade2k11598 ,  
  
I think all questions have been answered here.  
  
I'm going to close this issue off as the narrative is getting quite long and any more discussion will muddy the waters for future readers.  
  
If you have another query, feel free to post another issue or join us on Cpplang slack in the #beast channel:  
  
https://cppalliance.org/#links
