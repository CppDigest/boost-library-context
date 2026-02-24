# #9 Disable debug implementation by default [Merged]

> Username: Lastique  
> Created at: 2016-04-28 22:00:24 UTC  
> Updated at: 2016-08-12 04:56:28 UTC  
> Merged at: 2016-08-11 23:50:09 UTC  
> Closed at: 2016-08-11 23:50:09 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/9  

The debug implementation is not thread-safe, even if different threads are using separate iterators for reading elements of the container. BOOST_CB_DISABLE_DEBUG macro is no longer used, BOOST_CB_ENABLE_DEBUG=1 should be defined instead to enable debug support.  
  
Fixes https://svn.boost.org/trac/boost/ticket/6277.

---

## Comment 1

> Username: keryell  
> Created_at: 2016-05-02 08:27:19 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/9#issuecomment-216138263  

Nice! I've tried your version in  https://github.com/amd/triSYCL/commit/dd5adeb9d8954ebccf13b837e5a6d415752db490 and it works. So, for me, it looks ready to land.  
  
Thank you for your fix of this 4 year old bug!

---

## Comment 2

> Username: keryell  
> Created_at: 2016-08-09 09:27:03 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/9#issuecomment-238500968  

Ping!  
Anyone to review this pull request?  
Thank you.

---

## Comment 3

> Username: viboes  
> Created_at: 2016-08-10 22:45:40 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/9#issuecomment-239027801  

I have no rights to merge this pull request :(  
  
Can someone do it?

---

## Comment 4

> Username: glenfe  
> Created_at: 2016-08-12 00:57:07 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/9#issuecomment-239336418  

Done.

---

## Comment 5

> Username: viboes  
> Created_at: 2016-08-12 02:36:18 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/9#issuecomment-239347984  

Thanks.

---
