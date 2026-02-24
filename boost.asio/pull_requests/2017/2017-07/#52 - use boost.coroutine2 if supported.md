# #52 use boost.coroutine2 if supported [Closed]

> Username: olk  
> Created at: 2017-07-02 17:06:27 UTC  
> Updated at: 2017-09-30 06:36:35 UTC  
> Closed at: 2017-09-30 04:55:02 UTC  
> Url: https://github.com/boostorg/asio/pull/52  

- use boost.coroutine2 instead of deprecated boost.coroutine  
- boost.coroutine2 uses callcc() from boost.context,  
- boost.context defines BOOST_CONTEX_NO_CXX11 if callcc() can not be  
  be provided because of required C++11 are missing

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-07-31 20:20:53 UTC  
> Url: https://github.com/boostorg/asio/pull/52#issuecomment-319184803  

I'd like to see this in 1.66.0

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-07-31 21:40:26 UTC  
> Url: https://github.com/boostorg/asio/pull/52#issuecomment-319204598  

@olk What are we doing with Boost.Coroutine? It seems we deprecated a library currently in use by another Boost library, without offering a replacement. Boost.Coroutine2 requires C++11 if I am not mistaken so that needlessly breaks existing users.

---

## Comment 3

> Username: olk  
> Created_at: 2017-08-01 06:39:17 UTC  
> Url: https://github.com/boostorg/asio/pull/52#issuecomment-319282045  

@vinniefalco : we keep boost.coroutine as it is but encourage people to use boost.coroutine2

---

## Comment 4

> Username: dolphin8  
> Created_at: 2017-09-25 07:44:11 UTC  
> Url: https://github.com/boostorg/asio/pull/52#issuecomment-331802221  

It will be great good if asio supports Boost.Coroutine2. But I find out using Boost.Context bare mentally with asio is not so difficult. Here is my gist, (just for reference).[https://gist.github.com/dolphin8/f014a2ce861a858bbc995747ab96e9de](https://gist.github.com/dolphin8/f014a2ce861a858bbc995747ab96e9de)

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2017-09-25 16:45:00 UTC  
> Url: https://github.com/boostorg/asio/pull/52#issuecomment-331941408  

@dolphin8 Very nice!

---

## Comment 6

> Username: olk  
> Created_at: 2017-09-30 06:36:34 UTC  
> Url: https://github.com/boostorg/asio/pull/52#issuecomment-333288053  

replaced by PR #55 ('replace boost.coroutine by boost.context in spawn()')

---
