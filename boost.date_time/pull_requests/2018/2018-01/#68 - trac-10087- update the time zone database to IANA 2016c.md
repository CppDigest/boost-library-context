# #68 trac-10087: update the time zone database to IANA 2016c [Merged]

> Username: jeking3  
> Created at: 2018-01-06 16:41:55 UTC  
> Updated at: 2018-01-07 15:22:15 UTC  
> Merged at: 2018-01-07 15:22:15 UTC  
> Closed at: 2018-01-07 15:22:15 UTC  
> Url: https://github.com/boostorg/date_time/pull/68  

https://svn.boost.org/trac10/ticket/10087

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-01-06 16:53:55 UTC  
> Url: https://github.com/boostorg/date_time/pull/68#issuecomment-355759658  

What was the previous time zone database ? Can you give a link to this information ? Is it possible this change will break user's code ? I am all for this but I want to understand the ramifications.

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-01-06 16:56:06 UTC  
> Url: https://github.com/boostorg/date_time/pull/68#issuecomment-355759824  

Sorry I keep forgetting to add the link to the trac issue...  
  
https://svn.boost.org/trac10/ticket/10087

---

## Comment 3

> Username: jeking3  
> Created_at: 2018-01-06 18:24:38 UTC  
> Url: https://github.com/boostorg/date_time/pull/68#issuecomment-355765528  

@pdimov it looks like the automatic header code missed something in the failed build here.  Boost.Serialization is referring to Boost.Spirit which apparently wasn't pulled in by depinst.py:  
https://travis-ci.org/boostorg/date_time/jobs/325820754

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-01-06 18:46:57 UTC  
> Url: https://github.com/boostorg/date_time/pull/68#issuecomment-355766961  

https://travis-ci.org/boostorg/date_time/jobs/325820754#L528

---

## Comment 5

> Username: pdimov  
> Created_at: 2018-01-06 18:47:37 UTC  
> Url: https://github.com/boostorg/date_time/pull/68#issuecomment-355767010  

I probably need to check the return value :-)

---

## Comment 6

> Username: jeking3  
> Created_at: 2018-01-06 20:09:42 UTC  
> Url: https://github.com/boostorg/date_time/pull/68#issuecomment-355772555  

This change runs clean on all flavors of Windows and all linux I have tried.  The issue in the CI build is external to this PR.

---
