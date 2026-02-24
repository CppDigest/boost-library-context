# #318 - Process V2: Example fails to build when using BOOST_ASIO_SEPARATE_COMPILATION [Closed]

> Username: reddwarf69  
> Created at: 2023-06-10 17:11:47 UTC  
> Updated at: 2023-06-28 12:24:16 UTC  
> Closed at: 2023-06-28 12:24:16 UTC  
> Url: https://github.com/boostorg/process/issues/318  

As can be seen in [https://godbolt.org/z/q3r4hjGsd](https://godbolt.org/), https://github.com/boostorg/process/blob/develop/example/v2/intro.cpp fails to build when using BOOST_ASIO_SEPARATE_COMPILATION.  
  
(in general, not sure why asio::detail is used to start with)

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-06-13 12:52:54 UTC  
> Url: https://github.com/boostorg/process/issues/318#issuecomment-1589254748  

Did you post the correct godbolt link?

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-06-13 12:59:04 UTC  
> Url: https://github.com/boostorg/process/issues/318#issuecomment-1589264660  

FYI: This is a copy-pasta mistake. process.v2 was originally a PR to go into asio itself.
