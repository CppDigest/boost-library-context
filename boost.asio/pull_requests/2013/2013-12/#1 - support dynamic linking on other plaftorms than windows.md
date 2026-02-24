# #1 support dynamic linking on other plaftorms than windows [Closed]

> Username: arvidn  
> Created at: 2013-12-17 23:14:18 UTC  
> Updated at: 2017-05-27 20:08:25 UTC  
> Closed at: 2017-05-27 20:08:25 UTC  
> Url: https://github.com/boostorg/asio/pull/1  

the BOOST_ASIO_EXPORT macro is necessary to export a class (because in header-only mode "inline" would be inserted in a place it can't be). Leveraging boost.config for the export macro makes this support lots of other compilers. io_service::service needs to be exported on mac (and I believe linux) in order to have its typeinfo object be exported.

---
