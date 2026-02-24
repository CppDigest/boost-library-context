# #53 Fix ticket #12710: https://svn.boost.org/trac10/ticket/12710 [Closed]

> Username: justinasvd  
> Created at: 2017-08-31 12:43:17 UTC  
> Updated at: 2019-02-08 11:30:15 UTC  
> Closed at: 2019-02-08 11:30:15 UTC  
> Url: https://github.com/boostorg/asio/pull/53  

As it is, OpenSSL 1.1.0 SSL together with current Boost.Asio is simply unusable.  
  
This patch fixes a persistent stream_truncated error that prevents any reads.  
  
Note that OpenSSL team explains that one of the major changes between OpenSSL 1.0.2h and OpenSSL 1.1.0 was the rewritten SSL/TLS state machine, version negotiation, and record layer.  
  
See ​https://www.openssl.org/news/openssl-1.1.0-notes.html for reference.

---
