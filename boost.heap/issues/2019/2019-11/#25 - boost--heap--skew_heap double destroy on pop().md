# #25 - boost::heap::skew_heap double destroy on pop() [Closed]

> Username: kalaxy  
> Created at: 2019-11-06 22:01:54 UTC  
> Updated at: 2020-09-02 02:44:32 UTC  
> Closed at: 2020-09-02 02:44:31 UTC  
> Url: https://github.com/boostorg/heap/issues/25  

boost::heap::skew_heap::pop() destroys objects twice.   There is a call to the destructor ([skew_heap.hpp#L547](https://github.com/boostorg/heap/blob/19fda03545e1b85b3a2d3a94b9a3930ec64335d4/include/boost/heap/skew_heap.hpp#L547)) before the `#ifdef BOOST_NO_CXX11_ALLOCATOR` then another call to the destructor in both of the macro then, else branches.  
- [skew_heap.hpp#L549](https://github.com/boostorg/heap/blob/19fda03545e1b85b3a2d3a94b9a3930ec64335d4/include/boost/heap/skew_heap.hpp#L549)  
- [skew_heap.hpp#L553](https://github.com/boostorg/heap/blob/19fda03545e1b85b3a2d3a94b9a3930ec64335d4/include/boost/heap/skew_heap.hpp#L553)  
  
This looks like a regression from [commit 2b45bfef7f](https://github.com/boostorg/heap/commit/2b45bfef7f722b729a29419caa2c47be445a3e79).  
  
PS: I realize boost::heap is in maintenance mode, but I still wanted to track issues as I come across them.  Hopefully I can get around to submitting PRs, but at least awareness is out there.

---

## Comment 1

> Username: glenfe  
> Created at: 2020-09-02 02:44:31 UTC  
> Url: https://github.com/boostorg/heap/issues/25#issuecomment-685252949  

This was fixed in 4843f7af37ef06a0656be64e20206a3e8d1465d0.
