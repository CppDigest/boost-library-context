# #1247 - ssl_stream.hpp: outdated signature of set_verify_mode/set_verify_callback [Closed]

> Username: firewave  
> Created at: 2018-09-18 07:51:09 UTC  
> Updated at: 2018-11-27 16:35:03 UTC  
> Closed at: 2018-11-27 16:35:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1247  

The signature of `set_verify_mode` and `set_verify_callback` changed with boost 1.66, so using the ones of `ssl_stream` will cause a compilation failure.  
  
It's quite possible, that there are more mismatching signatures - I didn't check the whole file.  
  
https://www.boost.org/doc/libs/1_65_0/doc/html/boost_asio/reference/ssl__stream/set_verify_callback.html  
https://www.boost.org/doc/libs/1_68_0/doc/html/boost_asio/reference/ssl__stream/set_verify_callback.html  
  
https://www.boost.org/doc/libs/1_65_0/doc/html/boost_asio/reference/ssl__stream/set_verify_depth.html  
https://www.boost.org/doc/libs/1_68_0/doc/html/boost_asio/reference/ssl__stream/set_verify_depth.html

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-09-18 14:40:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1247#issuecomment-422420977  

If you want to submit a pull request, that would be an easy way to get your name into the list of contributors! Or I can do it, up to you.

---

## Comment 2

> Username: firewave  
> Created at: 2018-09-18 15:38:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1247#issuecomment-422443917  

The whole file probably needs to be reviewed and obviously some tests need to be written as well. Currently I just don't have the time to do that.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-09-18 16:11:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1247#issuecomment-422455054  

Hmm...I don't think we need tests, that's a bit extreme. This just passes through the calls to the underlying asio `ssl_stream`.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2018-11-17 17:05:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1247#issuecomment-439631686  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: octobanana  
> Created at: 2018-11-25 05:49:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1247#issuecomment-441417305  

This looks like it hasn't been resolved yet? If no one is currently working on it, I can submit a pull request for this.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-11-25 06:21:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1247#issuecomment-441418421  

That would be wonderful
