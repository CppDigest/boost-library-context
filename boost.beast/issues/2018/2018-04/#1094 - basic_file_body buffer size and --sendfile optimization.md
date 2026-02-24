# #1094 - basic_file_body buffer size and ::sendfile optimization [Open]

> Username: LubosD  
> Created at: 2018-04-12 17:50:48 UTC  
> Updated at: 2022-09-28 02:54:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1094  

Hi,  
  
I use Beast in an application which is to be mainly run on SoCs such as the Raspberry Pi.  
  
I have noticed that `boost::beast::file_body` suffers from VERY bad performance when sending large files. When I checked the code (Boost 1.66.0) I was surprised to see a very small buffer (4096 bytes) being used for reading the file, without the possibility of enlarging it easily. That's not nice at all.  
  
I googled and googled and noticed that sendfile support was mentioned a few times, but I didn't find it in either Boost 1.66.0 or the develop version of Beast.  
  
Is it hidden somewhere or what happened to it?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-04-12 18:39:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1094#issuecomment-380904678  

Use of `::SendFile` or `::TransmitFile` on Windows is automatic when `file_body` is an alias for `basic_file_body<file_win32>`. The code for it is here:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/http/impl/file_body_win32.ipp  
  
No efforts were made to optimize file bodies, so there is probably a lot of room for improvement especially on platforms which I have not personally developed on. If you would like to experiment with changing the buffer size or other improvements I would be more than happy to hear about the results and figure out how we can improve the interface or implementation to take advantage of it.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-05-11 01:23:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1094#issuecomment-388231907  

Beast is missing the implementation for using `::sendfile` on Linux. @chriskohlhoff submitted a patch but the BodyWriter concept changed afterwards and I don't have the facilities to fix it. I'm open to ideas on an interface for customizing the buffer size.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-05-11 04:21:38 UTC  
> Updated at: 2018-05-11 04:22:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1094#issuecomment-388255645  

How about if we have something like this interface:  
```  
serializer<false, file_body> sr{msg};  
...  
sr.writer_impl().buffer_size(1024 * 1024); // set one megabyte file read buffer  
...  
http::write(sock, sr);  
```  
You can adjust the buffer size after constructing the serializer and before writing the message.

---

## Comment 4

> Username: mzimbres  
> Created at: 2019-09-16 07:41:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1094#issuecomment-531672259  

It would be great to have this feature.

---

## Comment 5

> Username: jade2k11598  
> Created at: 2020-10-14 17:05:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1094#issuecomment-708536196  

Hi,  
Is there a timeline of when this feature would be made available?

---

## Comment 6

> Username: swex  
> Created at: 2020-12-29 10:17:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1094#issuecomment-752023923  

Tried to implement file_body_linux body on top of current development branch but it fails.  
can somebody more familiar with beast/asio explain me what is wrong.  
[Here](https://github.com/swex/beast/commit/07ae5002998116fcef297c7639e1a468aa50dfec) is my first try. I just copied `file_body_win32.hpp` and replaced `file_win32` with `file_linux` and `TransmitFile` with `sendfile`   But it doesn't work as expected...  
As I thought beast must call function below for async operations, but for some reason it calls just `get` function of body writer so its still userspace<->kernel spacecopy way..  
  
```c++  
template<  
    class Protocol, class Executor,  
    bool isRequest, class Fields>  
std::size_t  
write_some(  
    net::basic_stream_socket<  
        Protocol, Executor>& sock,  
    serializer<isRequest,  
        basic_file_body<file_linux>, Fields>& sr,  
    error_code& ec)  
```  
Can somebody help me what am I doing wrong?

---

## Comment 7

> Username: gyl30  
> Created at: 2022-09-28 02:54:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1094#issuecomment-1260320580  

ping
