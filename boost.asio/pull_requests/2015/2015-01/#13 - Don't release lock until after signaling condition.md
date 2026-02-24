# #13 Don't release lock until after signaling condition. [Closed]

> Username: jim-wong  
> Created at: 2015-01-27 08:54:13 UTC  
> Updated at: 2017-12-02 08:04:39 UTC  
> Closed at: 2017-12-02 08:04:39 UTC  
> Url: https://github.com/boostorg/asio/pull/13  

This change addresses a reproducible crash that we encountered. The details of the fix are described in the linked bug report.  
  
Original description (from bstrong):  
  
  This fixes a race in task_io_service where the event_object is  
  protected by the lock, and releasing the lock allows it to be deleted  
  before the condition is signaled.  
  
See  also: https://svn.boost.org/trac/boost/ticket/10213#comment:2

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2017-12-02 08:04:39 UTC  
> Url: https://github.com/boostorg/asio/pull/13#issuecomment-348676189  

This was fixed in boost 1.56.

---
