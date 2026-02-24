# #18 - Error handling questions [Closed]

> Username: bitbugprime  
> Created at: 2016-05-25 22:27:59 UTC  
> Updated at: 2016-06-12 20:07:05 UTC  
> Closed at: 2016-06-12 20:07:05 UTC  
> Url: https://github.com/boostorg/beast/issues/18  

In my code I catch boost::system::system_error. That works great for boost::asio::error::operation_aborted or boost::asio::error::eof, but since it looks like you map beast errors onto boost::system::system_error how do I distinguish the regular boost asio errors from beast http errors in my catch blocks? I don't know what boost::asio::error::whatever has the same value as what beast is throwing.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-05-25 23:27:34 UTC  
> Url: https://github.com/boostorg/beast/issues/18#issuecomment-221737382  

I believe you get the `error_code` out of the `system_error` and when you make the comparison using `error_code`, the implementation of `error_code` checks to see that the `error_category` associated with the `error_code` is the same as the `error_category` associated with the enum. Thats what user defined `make_error_code` is for, so the implementation of `error_code` can make the comparison. I believe it is okay for enum values from different error categories to overlap. See:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/error.ipp#L16  
  
Now I could be wrong about this, because the whole `error_code` and `system_error` thing is highly confusing and not particularly well explained.  
  
Note that for HTTP, only `basic_parser_v1` throws custom error codes, the rest will be the errors thrown by asio. `http::write` and `http::async_write` will return `boost::asio::error::eof` if the message indicates that the connection should be closed afterwards. I might change that.

---

## Comment 2

> Username: bitbugprime  
> Created at: 2016-05-25 23:47:44 UTC  
> Url: https://github.com/boostorg/beast/issues/18#issuecomment-221740493  

I've tested my server with junk input and it throws exceptions like "invalid method" (at least that's what an older version of beast did), which definitely aren't boost::asio::error::??? errors, but I can't figure out what error code namespace/name I should be checking for in my boost::system::system_error catch() block. I test for error::eof and error::operation_aborted, for example.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-05-25 23:54:35 UTC  
> Updated at: 2016-05-25 23:55:24 UTC  
> Url: https://github.com/boostorg/beast/issues/18#issuecomment-221741626  

This should work:  
  
```  
try  
{  
    ...  
}  
catch(boost::system::system_error const& se)  
{  
    if(se.code() == beast::http::parse_error::bad_method)  
        std::cerr << "You got a bad method!\n";  
    else if(se.code() == boost::asio::error::eof)  
        std::cerr << "End of file reached\n";  
    else  
        std::cerr << "Some other problem\n";  
}  
```  
  
Parse error codes are here:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/parse_error.hpp#L16

---

## Comment 4

> Username: bitbugprime  
> Created at: 2016-05-25 23:56:49 UTC  
> Url: https://github.com/boostorg/beast/issues/18#issuecomment-221741989  

Thanks. That must be relatively new, I don't recall seeing beast::http::parse_error::bad_method anywhere.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2016-05-25 23:59:55 UTC  
> Url: https://github.com/boostorg/beast/issues/18#issuecomment-221742479  

Ah yes, you're right about that. When Beast was using the nodejs http-parser, it had an `error_category` that pulled its strings from there:  
https://github.com/vinniefalco/Beast/blob/4cfa1d5cd34533caf4930455c6d3817b3bd00e89/include/beast/http/detail/error.hpp#L28

---

## Comment 6

> Username: vinniefalco  
> Created at: 2016-06-03 22:46:41 UTC  
> Url: https://github.com/boostorg/beast/issues/18#issuecomment-223712536  

There's a Gitter chat room for Beast available now:  
  
[![Join the chat at https://gitter.im/vinniefalco/Beast](https://badges.gitter.im/vinniefalco/Beast.svg)](https://gitter.im/vinniefalco/Beast?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
