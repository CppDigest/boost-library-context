# #817 - read_header gets only the body [Closed]

> Username: arun11299  
> Created at: 2017-10-16 10:11:06 UTC  
> Updated at: 2017-10-17 16:47:58 UTC  
> Closed at: 2017-10-16 10:56:11 UTC  
> Url: https://github.com/boostorg/beast/issues/817  

Hello,  
  
Beast version : 122  
Compiler: Clang 3.8  
Platform: Mac OSX  
  
As of now, I do not have an example to reproduce this issue. I though, I will just go ahead ask it here while trying to prepare a small example which reproduces this issue.  
  
What am I trying to do ?  
A simple download API using chunked encoding transfer in my existing framework.  
  
In principle, I am following the same mechanism as explained in the documentation for receiving chunks i.e. by creating an `empty_body` parser and passing it to the read calls.  
  
For the first call, i.e. when the buffer is empty I call the `beast::http::read_header` followed by the `beast::http::read` call. (The API which takes in parser as well).  
  
But the call fails with `bad_version` error since the parser is trying to parse the body (somehow only the body is there in the read buffer).  
  
Any clues ? Any better way to debug this in beast ? I checked the systemcalls using dtruss and setting up breakpoints in asio read calls just to see if I am doing any spurious reads, but that does not seem to be the case.  
  
  
Thanks.

---

## Comment 1

> Username: arun11299  
> Created at: 2017-10-16 10:16:15 UTC  
> Updated at: 2017-10-16 10:16:39 UTC  
> Url: https://github.com/boostorg/beast/issues/817#issuecomment-336843586  

Pasting dtruss output (the relevant parts only)  
```  
kevent(0x3, 0x7FFF59F53120, 0x1)		 = 0 0  
socket(0x2, 0x1, 0x6)		 = 6 0  
setsockopt(0x6, 0xFFFF, 0x1022)		 = 0 0  
connect(0x6, 0x7FFF59F52D80, 0x10)		 = 0 0  
sendmsg(0x6, 0x7FFF59F521A8, 0x0)		 = 126 0  
getrlimit(0x1008, 0x7FFF59F53270, 0x0)		 = 0 0  
fstat64(0x1, 0x7FFF59F53308, 0x0)		 = 0 0  
ioctl(0x1, 0x4004667A, 0x7FFF59F5334C)		 = 0 0  
write_nocancel(0x1, "\n\0", 0x1)		 = 1 0  
recvmsg(0x6, 0x7FFF59F530E8, 0x0)		 = 24 0  
stat64("/usr/lib/libstdc++.6.dylib\0", 0x7FFF59F52328, 0x0)		 = 0 0  
write_nocancel(0x2, "libc++abi.dylib: \0", 0x11)		 = 17 0  
write_nocancel(0x2, "terminating with uncaught exception of type boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::system::system_error> >: bad version\0", 0xA8)		 = 168 0  
write_nocancel(0x2, "\n\0", 0x1)		 = 1 0  
sigprocmask(0x3, 0x7FFF59F53004, 0x0)		 = 0x0 0  
__pthread_sigmask(0x3, 0x7FFF59F53010, 0x0)		 = 0 0  
__pthread_kill(0x603, 0x6, 0x0)		 = 0 0  
```

---

## Comment 2

> Username: arun11299  
> Created at: 2017-10-16 10:56:09 UTC  
> Url: https://github.com/boostorg/beast/issues/817#issuecomment-336852242  

Crap, just found the issue in server. Thats embarrassing.  
Closing this bug asap.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-10-16 14:01:06 UTC  
> Url: https://github.com/boostorg/beast/issues/817#issuecomment-336896115  

You might be one of the first people to use the interfaces which allow fine-grained access to chunk-encoded body data, please keep me informed about your experience it would be of great value - thanks!

---

## Comment 4

> Username: arun11299  
> Created at: 2017-10-16 14:09:43 UTC  
> Url: https://github.com/boostorg/beast/issues/817#issuecomment-336898584  

Thanks! Actually I am trying to create a python requests like library using beast. I am kind of slowly progressing because of other engagements, but till now the experience has been great.  
The only issue that has been is with debugging issues ( library user issues ), like where it failed in the response while parsing and things like that. Maybe it is already there and I am not aware of it.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-10-16 14:22:17 UTC  
> Url: https://github.com/boostorg/beast/issues/817#issuecomment-336902413  

I was thinking about writing a "debug stream" which sends a copy of its input or output to a `std::ostream` if your choice

---

## Comment 6

> Username: arun11299  
> Created at: 2017-10-17 05:40:50 UTC  
> Url: https://github.com/boostorg/beast/issues/817#issuecomment-337123540  

Yes, that is something I was thinking about too.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-10-17 15:23:29 UTC  
> Url: https://github.com/boostorg/beast/issues/817#issuecomment-337268483  

>I am trying to create a python requests like library using beast  
  
I am not sure if you realize what a massive undertaking that is. I have a roadmap for producing a library like Requests, but there are many moving parts that need to be assembled first. For example, just verifying the SSL certificate for a server against the operating system's list of root certificates is itself the subject of an entire library (and making it work on multiple platforms). And you need that verification component to do anything with https.

---

## Comment 8

> Username: arun11299  
> Created at: 2017-10-17 15:44:40 UTC  
> Updated at: 2017-10-17 15:44:48 UTC  
> Url: https://github.com/boostorg/beast/issues/817#issuecomment-337275329  

> I am not sure if you realize what a massive undertaking that is.   
  
Ha..I realized that while initially going through the python codebase. Right now my aim is to get a very stripped down version of the library ready and be able to do simple stuff. Also based on the time I am able to give to it, it would probably not reach anywhere. But, I am learning things :)

---

## Comment 9

> Username: arun11299  
> Created at: 2017-10-17 15:47:07 UTC  
> Url: https://github.com/boostorg/beast/issues/817#issuecomment-337276083  

Also, URI stuff is another thing. Since I did not want to use the cpp-netlib one, I wrote a basic one using spirit in the hope that one day we would get something in boost.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-10-17 16:47:58 UTC  
> Url: https://github.com/boostorg/beast/issues/817#issuecomment-337293803  

I'm working on a uri library at the moment
