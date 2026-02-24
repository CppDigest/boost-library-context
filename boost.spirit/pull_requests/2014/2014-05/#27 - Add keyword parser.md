# #27 Add keyword parser [Closed]

> Username: teajay-fr  
> Created at: 2014-05-07 12:22:39 UTC  
> Updated at: 2014-06-29 16:53:17 UTC  
> Closed at: 2014-06-29 16:53:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/27  

This adds a first functional version of the keywords parser.   
This is not feature complete yet. More will come.

---

## Comment 1

> Username: jamboree  
> Created_at: 2014-05-07 13:38:34 UTC  
> Updated_at: 2014-06-29 13:14:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/27#discussion_r12376471  

No need to use a functor for `keywords`, free function template is enough (just as `x3::grammar`).  
And I suggest to rename `keywords_directive` to `keywords_parser`, since `keywords_directive` is not a directive in any respect.  
  
You'd better use `remove_reference` or `x3::unrefcv_t` if you take the args by universal-ref (i.e. `Keywords&&`), to avoid store references in tuple which may lead to life-time issue in some cases.

---

## Comment 2

> Username: K-ballo  
> Created_at: 2014-05-08 01:38:18 UTC  
> Updated_at: 2014-06-29 13:14:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/27#discussion_r12412529  

What's this?

---

## Comment 3

> Username: teajay-fr  
> Created_at: 2014-05-08 05:15:50 UTC  
> Updated_at: 2014-06-29 13:14:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/27#discussion_r12415644  

This is code meant to be stripped out !  
There are some other parts of the tests that just don't work right now, but will. Here you are right. I can remove it.

---

## Comment 4

> Username: K-ballo  
> Created_at: 2014-05-08 13:35:27 UTC  
> Updated_at: 2014-06-29 13:14:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/27#discussion_r12428331  

If there are test cases that are not supported yet, please leave them (and use `//~ code` as the other tests do).

---

## Comment 5

> Username: teajay-fr  
> Created_at: 2014-06-29 16:53:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/27#issuecomment-47460132  

I messed up everything in this branch. Let me start over with a clean starting point.

---
