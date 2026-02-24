# #2189 - http::read throws "bad field" exception [Closed]

> Username: mjs476  
> Created at: 2021-03-14 18:31:26 UTC  
> Updated at: 2021-03-14 23:21:04 UTC  
> Closed at: 2021-03-14 23:21:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2189  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
If you rather keep your project secret, feel free  
to email the author at `<vinnie.falco@gmail.com>`; any  
private correspondence is treated as confidential.  
  
When reporting a bug please include the following:  
  
### Version of Beast  
  
```  
BOOST_BEAST_VERSION 181  
```  
  
### Steps necessary to reproduce the problem  
  
The following lines are throwing an exception:  
  
```c++  
beast::flat_buffer buffer;  
http::response<http::string_body> response;  
http::read(socket, buffer, response);  
```  
  
The response contains an `X-Remote-User` header, which is not represented in `field.hpp`. If I remove the header, the exception goes away.  
  
Is there a recommended way for the client to handle this? I suspect there is, but I couldn't easily find a doc that explains how.  
  
### All relevant compiler information  
  
Boost compiled with GCC 8.1, using shared libraries. Running on RHEL 7.8.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-03-14 18:33:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2189#issuecomment-798956443  

Beast is perfectly capable of reading messages with unknown fields, it should not throw an exception. Can you write a small test program which uses `beast::test::stream` and a static response string?

---

## Comment 2

> Username: mjs476  
> Created at: 2021-03-14 18:36:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2189#issuecomment-798956916  

It will require a bit of effort. The server is not a Beast HTTP server.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-03-14 18:41:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2189#issuecomment-798957494  

You dont need a server you can do it in just a few lines of code in `main` in a small program

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-03-14 18:41:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2189#issuecomment-798957588  

For example:  
https://github.com/boostorg/beast/blob/develop/test/beast/http/read.cpp#L582

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-03-14 18:46:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2189#issuecomment-798958341  

Here is sample code which tests your use-case:  
```  
        net::io_context ioc;  
        test::stream s(ioc);  
        s.append(  
            "HTTP/1.1 200 OK\r\n"  
            "Content-Length: 0\r\n"  
            "X-Remote-User: guest\r\n"  
            "\r\n");  
        flat_buffer b;  
        response<string_body> res;  
        read(s, b, res);  
}  
```

---

## Comment 6

> Username: mjs476  
> Created at: 2021-03-14 23:21:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2189#issuecomment-798999285  

OK, this was (unsuprisingly) a user error on my part.  
  
The server is using `http::fields` to write out the response headers. The server was essentially doing this:  
  
```c++  
http::fields fields;  
fields.set("X-Remote-User", "guest");  
  
for (const auto& field : fields)  
{  
    response << field.name() << ": " << field.value() << Newline;  
}  
```  
  
The issue was that `field.name()` returns `"<unknown-field>"` since X-Remote-User is not in the `field` enum. I switched to `field.name_string()` and it fixed the issue.
