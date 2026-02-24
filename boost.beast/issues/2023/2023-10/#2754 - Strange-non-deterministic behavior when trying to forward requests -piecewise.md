# #2754 - Strange/non-deterministic behavior when trying to forward requests "piecewise" [Closed]

> Username: davidchrista  
> Created at: 2023-10-23 15:58:39 UTC  
> Updated at: 2023-10-25 19:02:13 UTC  
> Closed at: 2023-10-25 19:02:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2754  

### Environment  
  
Version of Beast: 322  
OS: CentOS Linux 7.3.1611  
Compiler: GCC 8.3.1  
  
### Description  
  
Hi Beast team,  
  
I'm currently developing an asynchronous HTTP relay. The relay forwards all requests to another address. The forwarding happens "piecewise", that means:  
  
- First, the header is read. The information in the header is used to determine the forwarding target and other stuff. Also, the header will be adapted at this point (e.g., location field will be changed before forwarding)  
- Now a session with the forwarding target is established  
- The forwarding starts by sending the header  
- Afterwards, the forwarding of the body starts. If the body is bigger than a certain size, it will be forwarded in several pieces. That means, the piece is read from the incoming connection and forwarded. Then the next piece is read and also forwarded. And so on.  
- When forwarding is finished, the response from the forwarding target is read and again forwarded to the original client.  
  
Unfortunately, this only works sometimes, and I cannot find out what's the reason. In this picture you can see, that 5 of 9 attempts worked, and 4 failed. In this example I upload a file.  
  
![works_only_sometimes](https://github.com/boostorg/beast/assets/65857618/6a748948-b23e-4081-a416-021dfbda9751)  
  
The problem occurs after forwarding the header. The request times out when starting to read the body from the incoming request (at `return fail(ec, "onReadRequestBodyProgress");`).  
  
```c++  
// ...  
  
class PiecewiseForwarder : public Forwarder  
{  
public:  
// ...  
  
private:  
  static const unsigned int BODY_BUF_SIZE = 64 * 1024;  
  
  beast::tcp_stream &incStream_;  
  boost::optional<beast::tcp_stream> stream_;  
  char bodyBuf_[BODY_BUF_SIZE];  
  beast::flat_buffer readBuffer_;  
  
  http::request_parser<http::buffer_body> &parser_;  
  boost::optional<http::request_serializer<http::buffer_body>> serial_;  
};  
  
// ...  
  
void PiecewiseForwarder::onWriteHeader(beast::error_code ec, std::size_t)  
{  
  if (ec)  
    return fail(ec, "on_write_forward_header_imm");  
  
  assert(serial_->is_header_done());  
  
  readRequestBody();  
}  
  
void PiecewiseForwarder::readRequestBody()  
{  
  incStream_.expires_after(std::chrono::seconds(TIMEOUT));  
  stream_->expires_after(std::chrono::seconds(TIMEOUT));  
  
  parser_.get().body().data = bodyBuf_;  
  parser_.get().body().size = sizeof(bodyBuf_);  
  
  http::async_read(incStream_, readBuffer_, parser_,  
                   beast::bind_front_handler(&PiecewiseForwarder::onReadRequestBodyProgress,  
                                             shared_from_base<PiecewiseForwarder>()));  
}  
  
void PiecewiseForwarder::onReadRequestBodyProgress(beast::error_code ec,  
                                                   std::size_t bytesTransferred)  
{  
  if (ec == http::error::need_buffer)  
    ec = {};  
  if (ec)  
    return fail(ec, "onReadRequestBodyProgress");  
  
  parser_.get().body().data = bodyBuf_;  
  parser_.get().body().size = bytesTransferred;  
  
  if (parser_.is_done())  
    return onReadRequestBody(ec, bytesTransferred);  
  
  parser_.get().body().more = true;  
  
  incStream_.expires_after(std::chrono::seconds(TIMEOUT));  
  stream_->expires_after(std::chrono::seconds(TIMEOUT));  
  
  http::async_write(*stream_, *serial_,  
                    beast::bind_front_handler(&PiecewiseForwarder::onRequestBodyProgressSent,  
                                              shared_from_base<PiecewiseForwarder>()));  
}  
  
void PiecewiseForwarder::onRequestBodyProgressSent(beast::error_code ec, std::size_t)  
{  
  if (ec == http::error::need_buffer)  
    ec = {};  
  if (ec)  
    return fail(ec, "onRequestBodyProgressSent");  
  
  readRequestBody();  
}  
  
// ...  
```   
  
Do you have any idea, what I might be doing wrong?  
  
Thanks  
  
### Steps necessary to reproduce the problem  
  
- Using attached code  
- Some web service that accepts file uploads is required as a forwarding target  
- In Session::adaptTarget, the request target location must be adapted accordingly  
- In Session::handleRequestPiecewise the target host and port must be adapted  
- E.g. use cURL to upload a file  
  
[main.cpp.txt](https://github.com/boostorg/beast/files/13073093/main.cpp.txt)

---

## Comment 1

> Username: fpelliccioni  
> Created at: 2023-10-25 09:03:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2754#issuecomment-1778826981  

Thank you for the detailed report. To further assist and better understand the issue, could you please provide more details about the service you are using for file uploads? Knowing the specifics of that service would allow us to test under the same conditions.

---

## Comment 2

> Username: davidchrista  
> Created at: 2023-10-25 09:56:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2754#issuecomment-1778915687  

Sure, I'll create a proper test setup. Will take a few hours.

---

## Comment 3

> Username: davidchrista  
> Created at: 2023-10-25 10:57:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2754#issuecomment-1779011045  

So, I have created two repos.  
  
https://github.com/davidchrista/test_repo_boost_beast_issue_2754  
  
This is the main program where the bug/issue happens. I have cleaned it up a little bit to make it easier to understand, so it is different from the code I initially attached.  
  
https://github.com/davidchrista/boost_beast_file_upload_example  
  
This is the http_server_async Beast example with additional file upload functionality. It will be used as destination for the forwarded requests. Uploaded files will be written to the current directory.  
  
So, to reproduce the error, you have run both programs. The main program will start listening on Port 3000 and the forwarding destination will start listening on Port 3001. The main program is already configured to forward the requests to localhost:3001 (line 441).  
  
Then you have to do requests similar to  
  
```shell  
curl -X PUT -F "${f}=@${f}" $host:$port/$f  
```  
  
$f needs to be some file in the current directory which will be uploaded. $host and $port need to be set accordingly.  
  
If everything works as expected, the cURL requests will sometimes work and sometimes fail, like in the picture I've posted initially.  
  
Do you think you can work with this?  
  
Thank you very much for having a look at this issue!

---

## Comment 4

> Username: fpelliccioni  
> Created at: 2023-10-25 13:37:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2754#issuecomment-1779296931  

Thanks for the detailed description, I will take a look soon.

---

## Comment 5

> Username: ashtum  
> Created at: 2023-10-25 18:25:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2754#issuecomment-1779823957  

@davidchrista, I believe the problem is that you are using different instances of buffers on subsequent calls to async_read_header and async_read. You have to use the same buffer because async_read_header might read more data from the socket, which will be consumed in the subsequent call to async_read.  
  
It is also mentioned [in the documentation](https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_header.html#beast.ref.boost__beast__http__async_read_header.description):  
> The implementation may read additional bytes from the stream that lie past the end of the message being read. These additional bytes are stored in the dynamic buffer, which must be preserved for subsequent reads.

---

## Comment 6

> Username: davidchrista  
> Created at: 2023-10-25 19:02:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2754#issuecomment-1779878060  

Indeed, that really seems to solve the problem. I can't believe it ... I've struggled so many hours with this and now the solution is that simple.  
  
Thank you soooo much!
