# #134 Remove support for C++03 and prior compilers [Merged]

> Username: jefftrull  
> Created at: 2021-11-03 23:08:43 UTC  
> Updated at: 2021-12-08 18:49:48 UTC  
> Merged at: 2021-12-08 18:49:48 UTC  
> Closed at: 2021-12-08 18:49:48 UTC  
> Url: https://github.com/boostorg/wave/pull/134  

This removes only support for C++98/03 as a compiler for this library - Wave continues to support C++98 and C++03 constructs as a preprocessor.  
  
Support for C++98 and C++03 compilers was deprecated in 1.74, with a warning message.  
  
Doing this enables the use of C++11 constructs in the code, which should help maintainability.

---

## Comment 1

> Username: pdimov  
> Created_at: 2021-11-03 23:22:02 UTC  
> Url: https://github.com/boostorg/wave/pull/134#issuecomment-960289576  

No objections from me in principle, although I would have done that somewhere in the middle of the release cycle. Today is the deadline for the 1.78 beta, so this change is a bit too disruptive and is probably not entirely allowed by the release schedule (the cutoff for major changes was Oct 27, the cutoff for breaking changes Oct 20).

---

## Review 2 [Approved]

> Username: hkaiser  
> Created_at: 2021-11-04 00:50:27 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/134#pullrequestreview-797251371  

LGTM, thanks!

---

## Comment 3

> Username: jefftrull  
> Created_at: 2021-11-04 04:58:38 UTC  
> Url: https://github.com/boostorg/wave/pull/134#issuecomment-960459810  

Per discussion with @pdimov and @glenfe on cpplang Slack and here, this has arrived too late and so we'll do it next cycle. I'll leave this open until master reopens and do a big merge then (with Boost version info updated suitably).

---
