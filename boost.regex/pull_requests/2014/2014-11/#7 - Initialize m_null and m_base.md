# #7 Initialize m_null and m_base [Closed]

> Username: tempoz  
> Created at: 2014-11-11 18:25:53 UTC  
> Updated at: 2014-11-13 11:04:49 UTC  
> Closed at: 2014-11-13 11:04:49 UTC  
> Url: https://github.com/boostorg/regex/pull/7  

In the cases where we were not initializing m_null and m_base, instead we initialize them to default values. This simplifies the logic around assigning to them significantly and silences Coverity CID10550.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-11-13 10:29:44 UTC  
> Url: https://github.com/boostorg/regex/pull/7#issuecomment-62870936  

This breaks https://svn.boost.org/trac/boost/ticket/3632.  
  
Basically you cannot assign from a default-constructed (singular) iterator.  
  
Explicitly default constructing the remaining members will do no harm, but it has no positive effect either except to shut Coverty up of course.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2014-11-13 11:04:49 UTC  
> Url: https://github.com/boostorg/regex/pull/7#issuecomment-62875075  

See https://github.com/boostorg/regex/commit/ddd48205b8d46d23bcd0728f67856c9a855ed97b

---
