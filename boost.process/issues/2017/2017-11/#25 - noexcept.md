# #25 - noexcept [Closed]

> Username: garethsb-ghost  
> Created at: 2017-11-17 10:23:41 UTC  
> Updated at: 2017-12-19 20:29:17 UTC  
> Closed at: 2017-12-19 20:29:17 UTC  
> Url: https://github.com/boostorg/process/issues/25  

I'm just wondering whether it's intentional that Boost.Process doesn't support Visual Studio 2013 due to use of `noexcept` rather than `BOOST_NOEXCEPT`? (Someone raised this [in trac](https://svn.boost.org/trac10/ticket/13214) for VS2012, but it also applies to VS2013.)

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2017-11-17 17:45:23 UTC  
> Url: https://github.com/boostorg/process/issues/25#issuecomment-345313589  

Sort of - boost.process requires C++11 for `noexcept` and req-qualifiers. Now you can work around the first for VS2012, but the second will still bite you. So I don't see the point of using `BOOST_NOEXCEPT`.

---

## Comment 2

> Username: garethsb  
> Created at: 2017-11-17 18:04:24 UTC  
> Url: https://github.com/boostorg/process/issues/25#issuecomment-345318338  

Visual Studio 2013 RTM doesn't support ref-qualifiers, although the CTP Nov 2013 update does I think. But if the answer is Boost.Process supports VS 2015 minimum, that's a clear statement, thanks.  
  
(I was initially only looking to use cross platform boost::this_process::get_id() anyway.)

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2017-11-17 19:36:24 UTC  
> Url: https://github.com/boostorg/process/issues/25#issuecomment-345345151  

Well I said I don't see the point not that there isn't a case. If you have a workaround that doesn't impact the current design (as in removing ref-qualifiers) but gives you an application (like this_process) feel free to submit a PR. I just won't consider that supported. Btw. I don't think in terms of compiler support, but language and platform support, i.e. it's C++11 and posix or windows, that I aim to support.

---

## Comment 4

> Username: garethsb  
> Created at: 2017-11-17 19:41:38 UTC  
> Url: https://github.com/boostorg/process/issues/25#issuecomment-345346465  

OK, thanks.  
  
I prefer to think in terms of language revisions too, but unfortunately it's compilers that I have to work with. :-)
