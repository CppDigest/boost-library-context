# #2 Add visibility support for Boost.Asio. Fixes #2114 [Closed]

> Username: jhunold  
> Created at: 2014-01-04 10:02:55 UTC  
> Updated at: 2014-06-23 10:44:11 UTC  
> Closed at: 2014-01-26 08:47:51 UTC  
> Url: https://github.com/boostorg/asio/pull/2  

Found while cleaning up my git svn mirror after git conversion. Add visibility support for gcc and clang using common macros from Boost.Config

---

## Comment 1

> Username: arvidn  
> Created_at: 2014-01-24 21:22:49 UTC  
> Url: https://github.com/boostorg/asio/pull/2#issuecomment-33262537  

you actually also need to export:  
  
   class io_service::service  
  
Otherwise you'll get "undefined reference to `typeinfo for boost::asio::io_service::service'". See my pull request https://github.com/boostorg/asio/pull/1

---

## Comment 2

> Username: jhunold  
> Created_at: 2014-01-26 08:47:51 UTC  
> Url: https://github.com/boostorg/asio/pull/2#issuecomment-33312305  

Well,  my patch is quite old and only tested locally on Linux.

---
