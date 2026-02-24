# #203 Fixes for range segment iterators [Merged]

> Username: mkaravel  
> Created at: 2015-01-29 21:20:22 UTC  
> Updated at: 2015-02-03 20:05:03 UTC  
> Merged at: 2015-01-29 21:23:10 UTC  
> Closed at: 2015-01-29 21:23:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/203  

This PR is about two fixes:  
- Declare the `range_segment_iterator` as a class so as to match the friend declaration inside it (to avoid relevant warning)  
- Fix bug in initialization of the `m_has_less_than_two_elements` boolean member variable for open ranges. When the range is open (e.g., when the range is a open ring, or one of the rings of an open polygon), the actual size of the range is increased by one (due to the closing iterator). So whether the actual (equivalent closed) range has less than 2 elements amounts to asking whether the original range is empty (has less then 1 element).

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-01-29 21:22:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/203#discussion_r23803729  

Good you catched this.  
Thanks for the quick fix.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-01-29 21:25:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/203#discussion_r23803896  

The unit test did the catch :-)  
I simply applied the fix.

---
