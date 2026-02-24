# #1588 - "body limit exceeded" error during http::async_read with no parser [Closed]

> Username: ADDubovik  
> Created at: 2019-04-23 10:34:54 UTC  
> Updated at: 2019-04-24 09:25:08 UTC  
> Closed at: 2019-04-24 09:25:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1588  

### Version of Beast  
BOOST_BEAST_VERSION 189  
  
### Steps necessary to reproduce the problem  
Try to get a big HTTP response.  
  
### All relevant compiler information  
Microsoft Visual Studio Community 2017 Version 15.9.11  
  
  
I have a "body limit exceeded" error during http::async_read.  
I know about parser default body limit, but I do not use parser (or it's somewhere under the hood?).  
  
`auto buf = std::make_shared<boost::beast::multi_buffer>();  
auto resp = std::make_shared<boost::beast::http::response< boost::beast::http::dynamic_body > >();  
  
boost::beast::http::async_read  
(  
  m_ssl->getStream(),  
  *buf,  
  *resp,  
  [shis, buf, resp](boost::system::error_code const & ec, auto)  
  {  
    if ( shis.use_count() > 1 )  
      shis->onEventRead(ec, resp);  
  }  
);  
`

---

## Comment 1

> Username: djarek  
> Created at: 2019-04-23 10:40:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1588#issuecomment-485750690  

When you use the message-oriented interface for reading/wriiting HTTP message, you use the parser's defaults, which are: `The default limit is 1MB for requests and 8MB for responses.`. If you want to transfer larger bodies, you need to use the parser-oriented overloads.

---

## Comment 2

> Username: ADDubovik  
> Created at: 2019-04-23 10:41:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1588#issuecomment-485750963  

Is it possible to tune message-oriented interface?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-04-23 13:29:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1588#issuecomment-485803131  

> Is it possible to tune message-oriented interface?  
  
No.
