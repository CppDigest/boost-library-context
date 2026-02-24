# #2751 - EOF error when calling http::write after accepting connection in the server [Closed]

> Username: dellarocca99  
> Created at: 2023-10-18 20:20:24 UTC  
> Updated at: 2023-10-22 13:43:09 UTC  
> Closed at: 2023-10-22 13:43:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2751  

Hi @vinniefalco !  
  
I'm working on a server that uses an old version of Beast, with Boost 1.59.0.  
The content of version.hpp is the following:  
  
```c++  
//  
// Copyright (c) 2013-2016 Vinnie Falco (vinnie dot falco at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
  
#ifndef BEAST_VERSION_HPP  
#define BEAST_VERSION_HPP  
  
// follows http://semver.org  
  
//  BEAST_VERSION % 100 is the patch level  
//  BEAST_VERSION / 100 % 1000 is the minor version  
//  BEAST_VERSION / 100000 is the major version  
//  
#define BEAST_VERSION 100000  
  
#define BEAST_VERSION_STRING "1.0.0-b13"  
  
#endif  
```   
  
This server was working fine with an nw.js browser. However, I changed this nw.js browser to a newer one, and now I'm facing the following error: when I accept the connection from the client, it crashes with the error "End of File".   
  
To be more specific, it throws the error when execution reaches the line 538 of write.ipp. The method is "write" and it looks like this:  
  
```c++  
template<class SyncWriteStream,  
    bool isRequest, class Body, class Headers>  
void  
write(SyncWriteStream& stream,  
    message_v1<isRequest, Body, Headers> const& msg,  
        boost::system::error_code& ec)  
{  
    static_assert(is_SyncWriteStream<SyncWriteStream>::value,  
        "SyncWriteStream requirements not met");  
    static_assert(is_WritableBody<Body>::value,  
        "WritableBody requirements not met");  
    detail::write_preparation<isRequest, Body, Headers> wp(msg);  
    wp.init(ec);  
    if(ec)  
        return;  
    std::mutex m;  
    std::condition_variable cv;  
    bool ready = false;  
    resume_context resume{  
        [&]  
        {  
            std::lock_guard<std::mutex> lock(m);  
            ready = true;  
            cv.notify_one();  
        }};  
    auto copy = resume;  
    boost::tribool result = wp.w(std::move(copy),  
        ec, detail::writef0_lambda<SyncWriteStream,  
            decltype(wp.sb)>{stream, wp.sb, wp.chunked, ec});  
    if(ec)  
        return;  
    if(boost::indeterminate(result))  
    {  
        copy = resume;  
        {  
            std::unique_lock<std::mutex> lock(m);  
            cv.wait(lock, [&]{ return ready; });  
            ready = false;  
        }  
        boost::asio::write(stream, wp.sb.data(), ec);  
        if(ec)  
            return;  
        result = false;  
    }  
    wp.sb.consume(wp.sb.size());  
    if(! result)  
    {  
        for(;;)  
        {  
            result = wp.w(std::move(copy), ec,  
                detail::writef_lambda<SyncWriteStream>{  
                    stream, wp.chunked, ec});  
            if(ec)  
                return;  
            if(result)  
                break;  
            if(! result)  
                continue;  
            copy = resume;  
            std::unique_lock<std::mutex> lock(m);  
            cv.wait(lock, [&]{ return ready; });  
            ready = false;  
        }  
    }  
    if(wp.chunked)  
    {  
        // VFALCO Unfortunately the current interface to the  
        //        Writer concept prevents us from using coalescing the  
        //        final body chunk with the final chunk delimiter.  
        //  
        // write final chunk  
        boost::asio::write(stream, detail::chunk_encode_final(), ec);  
        if(ec)  
            return;  
    }  
    if(wp.close)  
    {  
        // VFALCO TODO Decide on an error code  
        ec = boost::asio::error::eof;  
    }  
}  
```  
  
It breaks when wp.close is true and then it throws the error.  
I'm using VS2017 and MSVC v140. I'm compiling Beast code with my project as it is not present in Boost 1.59.0  
  
Thanks in advance!!!

---

## Comment 1

> Username: fpelliccioni  
> Created at: 2023-10-18 21:41:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2751#issuecomment-1769364941  

Hello,  
  
From the provided version.hpp, it's challenging to identify the exact Beast version, but it appears you're using an older one. Linking directly to the specific Beast files on GitHub can help us pinpoint the issue more efficiently.  
  
To further investigate and reproduce the problem, please provide:  
  
- Complete platform details.  
- Source code of the Beast server application.  
- Source code of the client-side JavaScript.  
- Compilation method for the C++ code.  
- Execution method for the JS code.  
  
Additionally, I recommend updating to a more recent version of Beast as a preliminary step, as this might resolve the issue and offer additional improvements.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2023-10-21 11:22:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2751#issuecomment-1773761032  

1.0.0-b13 is unsupported  
  
@fpelliccioni I don't think any of that information is going to help us

---

## Comment 3

> Username: fpelliccioni  
> Created at: 2023-10-22 13:43:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2751#issuecomment-1774099453  

Good to know, thanks.  
So @dellarocca99, I have to close the issue becase the version you are using is unsupported.
