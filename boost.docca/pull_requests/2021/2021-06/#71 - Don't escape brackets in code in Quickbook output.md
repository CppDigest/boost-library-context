# #71 Don't escape brackets in code in Quickbook output [Closed]

> Username: evanlenz  
> Created at: 2021-06-24 03:44:00 UTC  
> Updated at: 2021-07-02 23:01:16 UTC  
> Closed at: 2021-07-02 22:59:35 UTC  
> Url: https://github.com/boostorg/docca/pull/71  

fix #69

---

## Comment 1

> Username: evanlenz  
> Created_at: 2021-06-24 03:45:39 UTC  
> Updated_at: 2021-06-24 03:45:52 UTC  
> Url: https://github.com/boostorg/docca/pull/71#issuecomment-867311949  

One of the two fixes in this pull request addresses a rather nasty regression (reported in issue #69). It was being exploited 50+ times in the JSON project and 20+ times in the Beast project.

---

## Comment 2

> Username: madmongo1  
> Created_at: 2021-07-02 14:50:52 UTC  
> Url: https://github.com/boostorg/docca/pull/71#issuecomment-873056857  

I see no issues in beast docs when applying this PR. Are you able to point me to an example of the problem so I can check it´s fixed?

---

## Comment 3

> Username: evanlenz  
> Created_at: 2021-07-02 18:51:19 UTC  
> Url: https://github.com/boostorg/docca/pull/71#issuecomment-873197598  

@madmongo1 Here are a few fixed examples:  
https://www.boost.org/doc/libs/1_76_0/libs/json/doc/html/json/ref/boost__json__object/operator_lb__rb_.html  
https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_fields/operator_lb__rb_/overload1.html  
https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/ref/boost__beast__has_get_executor.html  
https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_handshake/overload2.html  
  
However, hold off on accepting it, because I just noticed a possible regression (missing space character before `executor_type`) on this page:  
https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/ref/boost__beast__test__basic_stream/async_write_some.html  
  
Stay tuned...

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2021-07-02 19:22:40 UTC  
> Url: https://github.com/boostorg/docca/pull/71#issuecomment-873213389  

@madmongo1 I see `BOOST_ASIO_DEFAULT_COMPLETION_TOKEN_TYPE` in the docs, users will probably get confused by this. What is the purpose of this macro? Do we want to redefine it for generating docs (perhaps to the empty string)? What does it look like rendered in asio?

---

## Comment 5

> Username: evanlenz  
> Created_at: 2021-07-02 22:59:29 UTC  
> Url: https://github.com/boostorg/docca/pull/71#issuecomment-873294015  

Actually, I will squash these commits into a single commit and open a new pull request to be safe...

---
