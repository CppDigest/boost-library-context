# #1992 - Right way to download large files using async http ssl [Closed]

> Username: ManojNisal  
> Created at: 2020-06-21 05:27:57 UTC  
> Updated at: 2020-11-16 03:45:59 UTC  
> Closed at: 2020-11-16 03:45:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1992  

I'm trying to write a program to download multiple files over http ssl asynchronously.  
I'm basing this on https://www.boost.org/doc/libs/1_72_0/libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp.  
  
For smaller files, say up to 10 or 20 MB, the full file is available in http response (string body). But this won't be suitable for large files of size greater than GB or so. The way I'm handling this is by specifying range request for each http request. I'm making multiple such http requests in a single io context.  
  
Now, the problem is, some of the http responses return partial read response and there is no content in body. This happens randomly for any of the http requests within a session. Not sure if there is some problem in the way I'm handling this case.  
  
Here is what I'm doing:  
  
for(until the file read requests cover all the extents of file)  
{  
   auto session_ptr = make_shared<session>(ioc, ctx);  
  session_ptr->SetRange(offset, nBytes); // this sets http range field to value like "bytes=0-1023"  
  session_ptr->run(host.c_str(), port, target.c_str(), 11);  
}  
ioc.run();  
  
Note that this works fine if the number of requests in io context is less, say two or three.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-06-21 18:08:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1992#issuecomment-647161822  

I'm going to need to see some code. It sounds as if you have a logic error somewhere.

---

## Comment 2

> Username: ManojNisal  
> Created at: 2020-06-22 07:36:50 UTC  
> Updated at: 2020-06-22 07:44:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1992#issuecomment-647342522  

I'm reusing the session class from https://www.boost.org/doc/libs/1_72_0/libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp.  
  
Changes that I have added to session class are:  
  
1.  A method to set range.  
`void session::SetRange(uint64_t offset, uint64_t numBytes)  
{  
    std::stringstream stream;  
    stream << "bytes=" << offset << "-" << offset + numBytes - 1;  
    range_ = stream.str();  
}`  
  
  
2. in session::run, set the http range field  
`if (!range_.empty())  
{  
        req_.set(http::field::range, range_);  
}  
`  
3. Set the timeout to 1 second instead of 30 secs before calling async_shutdown  
  
In the class which downloads the files,  
  
`int requestBufferSize = 1024 * 1024 * 2; // 2 MB  
string target, host;  
ParseURL(item.downloadUrl, host, target);   
  
uint64_t numBytesRead = 0;  
uint64_t offset = 0;`  
  
`while (numBytesRead < item.size) // item is of a type that i have defined and stores its size, id, download URL and so on  
{  
                offset = numBytesRead;  
                uint32_t numBytesRequested = requestBufferSize;  
  
                if ((item.size - numBytesRead) < requestBufferSize)  
                    numBytesRequested = item.size - numBytesRead;  
  
                auto session_ptr = make_shared<session>(ioc, ctx);  
                session_ptr->SetRange(offset, numBytesRequested);  
  
                listExtentRequests.push_back(session_ptr); // this can be ignored, using it to track session requests and extents  
                session_ptr->run(host.c_str(), port, target.c_str(), 11);  
                numBytesRead += numBytesRequested;  
}  
ioc.run();`  
  
  
  
This works fine if the number of requests is less.

---

## Comment 3

> Username: ManojNisal  
> Created at: 2020-06-23 11:07:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1992#issuecomment-648075350  

Update:  
  
If i change requestBufferSize(i.e. range requested per request) to 200KB and make 100 concurrent requests in a single io context, things work fine.

---

## Comment 4

> Username: ManojNisal  
> Created at: 2020-07-10 06:30:21 UTC  
> Updated at: 2020-07-10 06:30:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1992#issuecomment-656508320  

Any suggestions on this? What could be the reason for partial read response in beast?

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-07-10 06:43:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1992#issuecomment-656512621  

Hi @ManojNisal ,  
  
Is there a specific reproducible bug you’d like to report?  
  
If it’s something that I can prove in a test, it becomes a lot easier to fix and maintain.

---

## Comment 6

> Username: ManojNisal  
> Created at: 2020-07-10 11:04:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1992#issuecomment-656618390  

Hi @madmongo1 ,  
  
If I make multiple async http requests over ssl using beast, to download a large file in chunks, then some of the range requests do not return any data. Instead they return with error code "partial read".

---

## Comment 7

> Username: madmongo1  
> Created at: 2020-07-10 11:22:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1992#issuecomment-656624974  

Do you mean beast::http::error::partial_message or asio::ssl::error::short_read?  
  
Can you show some code?

---

## Comment 8

> Username: ManojNisal  
> Created at: 2020-07-10 11:44:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1992#issuecomment-656632363  

Right, the error is beast::http::error::partial_message.  
The code is as mentioned in comment #3 above. It is based on the async https example from the library.  
  
Thanks

---

## Comment 9

> Username: madmongo1  
> Created at: 2020-07-10 12:17:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1992#issuecomment-656645380  

@ManojNisal With respect, the code may be based on a tested example, but it now does something different.  
We are generally our own worst code reviewer (including me).   
If you put your code/project in a github repo I will compile it and take a look.  
What I will probably do is set breakpoints around the failure and examine what is exactly going on at that point. Then I will write a unit test to ensure that what is happening is what is expected.  
This may involve the use of wireshark to capture that actual raw data coming from the server.  
This would allow me to express the problem in terms of :  
a) a bug in beast, or  
b) a programming error  
  
If you could do the above it would be instructive for us both.

---

## Comment 10

> Username: ManojNisal  
> Created at: 2020-07-12 06:45:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1992#issuecomment-657182635  

Hi @madmongo1 ,  
I have stripped down the problem into a basic test application with a single source file which is attached here.  
It just tries to download a Debian iso in batches of range requests. In the file, I'm trying to make 100 concurrent requests of 2MB each, at a time, until required bytes are downloaded. You can tune the parameters as needed.   
Thanks again for all the help.  
  
[Source.cpp.zip](https://github.com/boostorg/beast/files/4908126/Source.cpp.zip)

---

## Comment 11

> Username: stale[bot]  
> Created at: 2020-08-16 07:19:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1992#issuecomment-674490942  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 12

> Username: ManojNisal  
> Created at: 2020-08-17 16:14:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1992#issuecomment-674973596  

This looks similar to https://github.com/boostorg/beast/issues/2015

---

## Comment 13

> Username: stale[bot]  
> Created at: 2020-09-20 03:28:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1992#issuecomment-695613711  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 14

> Username: madmongo1  
> Created at: 2020-09-21 06:02:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1992#issuecomment-695917008  

Keepalive

---

## Comment 15

> Username: stale[bot]  
> Created at: 2020-11-01 07:41:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1992#issuecomment-720048920  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 16

> Username: stale[bot]  
> Created at: 2020-11-16 03:45:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1992#issuecomment-727717746  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
