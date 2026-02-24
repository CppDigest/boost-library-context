# #22 Run config_test and config_info with no RTTI and no exceptions.  [Merged]

> Username: pdimov  
> Created at: 2014-06-08 00:29:32 UTC  
> Updated at: 2014-06-08 20:22:34 UTC  
> Merged at: 2014-06-08 08:40:21 UTC  
> Closed at: 2014-06-08 08:40:21 UTC  
> Url: https://github.com/boostorg/config/pull/22  

This pull request just adds to `test/Jamfile.v2` `config_test` and `config_info` with `<rtti>off` and `<exception-handling>off` so that we can see whether `BOOST_NO_RTTI`, `BOOST_NO_TYPEID` and `BOOST_NO_EXCEPTIONS` are being set properly.

---
