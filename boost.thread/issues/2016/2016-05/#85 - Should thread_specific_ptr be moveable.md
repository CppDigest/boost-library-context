# #85 - Should thread_specific_ptr be moveable? [Closed]

> Username: daniel-j-h  
> Created at: 2016-05-20 23:37:55 UTC  
> Updated at: 2018-10-09 04:40:28 UTC  
> Closed at: 2018-10-09 04:40:28 UTC  
> Url: https://github.com/boostorg/thread/issues/85  

Ticketing http://lists.boost.org/boost-users/2015/12/85442.php: `boost::thread_specific_ptr` currently [declares its copy members privately](https://github.com/boostorg/thread/blob/382ac5a4269045fc5431c2f445c430f10f5b0420/include/boost/thread/tss.hpp#L34-L35) which prevents moveability.  
  
From what I can tell, a move should be possible along the lines of:  
  
``` c++  
auto* p = other.release(); // without invoking other's cleanup fn  
reset(p); // potentially different cleanup fn associated  
```  
  
The only problematic situation I can think of is a scenario with two  
semantically different custom cleanup function where a move eventually  
triggers only the second one. But this is behavior is consistent with  
move semantic.  
  
/cc @viboes

---

## Comment 1

> Username: viboes  
> Created at: 2017-08-23 07:02:06 UTC  
> Url: https://github.com/boostorg/thread/issues/85#issuecomment-324240087  

A PR is welcome

---

## Comment 2

> Username: Kojoley  
> Created at: 2018-10-04 22:35:13 UTC  
> Url: https://github.com/boostorg/thread/issues/85#issuecomment-427191444  

Current implementation uses `thread_specific_ptr` self memory address as an unique identifier for a variable. It is possible to make `thread_specific_ptr` movable, but is there any rationale for it? The change will require updating information in other threads so it will use either locking (not cool) or atomics (not all systems provide them).

---

## Comment 3

> Username: viboes  
> Created at: 2018-10-09 04:40:28 UTC  
> Url: https://github.com/boostorg/thread/issues/85#issuecomment-428057355  

Closed as no PR provided since more than 1 year and it is not clear what a movable thread_specific_ptr could be
