# #209 - Legal problem with `win32/thread_primitives.hpp` [Closed]

> Username: pdimov  
> Created at: 2018-02-20 14:53:53 UTC  
> Updated at: 2018-02-26 15:57:39 UTC  
> Closed at: 2018-02-26 15:22:55 UTC  
> Url: https://github.com/boostorg/thread/issues/209  

A third-party legal review has identified a problem as reported here:  
  
https://lists.boost.org/Archives/boost/2018/02/241435.php  
  
regarding  
  
https://github.com/boostorg/thread/blob/526c72cb4b8e3cb14d6f8e008006ef36b2be2c39/include/boost/thread/win32/thread_primitives.hpp#L89-L110  
  
introduced by  
  
https://github.com/boostorg/thread/commit/04c53415fd0adc1921d419c54a1e5ec4b19917ea  
  
The easiest way to fix that is to rewrite `GetTickCount64emulation` in terms of `boost::atomic_uint64_t`. As pointed out in  
  
https://lists.boost.org/Archives/boost/2018/02/241453.php  
  
an implementation already exists in Boost.Log:  
  
https://github.com/boostorg/log/blob/1cc577cbf5fae8f55c71c4493a7ef89027bd85dc/src/timestamp.cpp#L66-L86

---

## Comment 1

> Username: viboes  
> Created at: 2018-02-20 16:59:29 UTC  
> Url: https://github.com/boostorg/thread/issues/209#issuecomment-367044513  

I missed the comment on this commit.  
  
could someone provide a PR?

---

## Comment 2

> Username: pdimov  
> Created at: 2018-02-20 17:19:25 UTC  
> Url: https://github.com/boostorg/thread/issues/209#issuecomment-367050908  

@Lastique perhaps?

---

## Comment 3

> Username: Lastique  
> Created at: 2018-02-20 19:36:20 UTC  
> Url: https://github.com/boostorg/thread/issues/209#issuecomment-367093519  

I will make a PR shortly.

---

## Comment 4

> Username: viboes  
> Created at: 2018-02-20 20:08:54 UTC  
> Url: https://github.com/boostorg/thread/issues/209#issuecomment-367103193  

Thank you very much.
