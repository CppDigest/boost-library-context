# #6 Replace Boost.Test with lightweight_test from Boost.Core [Closed]

> Username: mloskot  
> Created at: 2018-08-28 14:12:44 UTC  
> Updated at: 2019-07-10 23:03:46 UTC  
> Closed at: 2019-07-10 22:26:32 UTC  
> Url: https://github.com/boostorg/array/pull/6  

The tests do not require fully-featured testing framework.  
The [lightweight_test](https://www.boost.org/doc/libs/develop/libs/core/doc/html/core/lightweight_test.html) alternative should also save on build times.  
  
-----  
  
Motivation is drawn from recent discussions on #boost channel at cpplang.slack.com, about preferring the lightweight_test wherever possible, also to cut number of inter-dependencies in Boost.

---

## Comment 1

> Username: mclow  
> Created_at: 2018-08-28 14:19:46 UTC  
> Url: https://github.com/boostorg/array/pull/6#issuecomment-416602899  

@mloskot - the problem with discussions on the slack channel (as opposed to the mailing list) is that they are not archived, and can't be reviewed later.

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-08-28 14:29:46 UTC  
> Updated_at: 2018-08-28 14:30:28 UTC  
> Url: https://github.com/boostorg/array/pull/6#issuecomment-416606901  

@mclow Right. However, I think that even if I did not mention it, the rationale behind the change should be clear enough.

---

## Comment 3

> Username: pdimov  
> Created_at: 2018-08-28 14:41:57 UTC  
> Url: https://github.com/boostorg/array/pull/6#issuecomment-416612068  

You forgot to `return boost::report_errors();` :-)

---

## Comment 4

> Username: mclow  
> Created_at: 2018-08-28 14:46:54 UTC  
> Url: https://github.com/boostorg/array/pull/6#issuecomment-416614235  

It's not the motivation that I'm missing, but rather the discussion of the pros and the cons.

---

## Comment 5

> Username: mloskot  
> Created_at: 2018-08-28 15:01:26 UTC  
> Updated_at: 2018-08-28 15:01:39 UTC  
> Url: https://github.com/boostorg/array/pull/6#issuecomment-416620378  

@pdimov Thank you. I've updated the pull request.  
  
@mclow The discussion was not very extensive, just that for simple needs lightweight_test should be enough, less dependency may decrease overall maintenance hassle, and such.  
  
Quoting @mjcaisse   
> Boost.Test is in a peculiar position of being a dependency for so many pre-c++11 supporting libraries. As such it has a different set of constraints.

---

## Comment 6

> Username: mclow  
> Created_at: 2019-07-10 22:26:32 UTC  
> Url: https://github.com/boostorg/array/pull/6#issuecomment-510254272  

Committed a modified version of this as 453cf59

---
