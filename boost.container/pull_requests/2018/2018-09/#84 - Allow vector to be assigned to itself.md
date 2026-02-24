# #84 Allow vector to be assigned to itself [Closed]

> Username: Timmmm  
> Created at: 2018-09-11 11:37:08 UTC  
> Updated at: 2019-04-30 16:07:29 UTC  
> Closed at: 2019-04-30 16:07:28 UTC  
> Url: https://github.com/boostorg/container/pull/84  

This sounds like an odd thing to do but some implementations of `std::random_shuffle()` do this because they call `std::swap(x, x)` (and cppreference implies that they are allowed to). Old versions of libstdc++ used to do this but now they don't. The current as-of-now version of libc++ does it for one variant of `std::random_shuffle()` but not others. I have opened [a bug](https://bugs.llvm.org/show_bug.cgi?id=38900) for them to fix that, but `swap(a, a)` should still be allowed anyway.  
  
Quoting [cppreference](https://en.cppreference.com/w/cpp/named_req/Swappable):  
  
> C++ named requirements: Swappable  
> Any lvalue or rvalue of this type can be swapped with any lvalue or rvalue of some other type  
  
Also [relevant SO question](https://stackoverflow.com/questions/24444630/is-stdswapx-x-guaranteed-to-leave-x-unchanged)

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2018-11-10 23:47:32 UTC  
> Updated_at: 2018-11-10 23:52:44 UTC  
> Url: https://github.com/boostorg/container/pull/84#issuecomment-437630984  

I can't see why std::swap should fail, as it would use a temporary vector to do the move assignment.   
  
Edit: I see the assertion would fire. But I don't like the idea of pessimizing the vector move operation with a branch. Maybe I should simply allow it with empty vectors.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2018-11-11 00:12:29 UTC  
> Url: https://github.com/boostorg/container/pull/84#issuecomment-437632222  

I've added a commit with some changes:   
  
https://github.com/boostorg/container/commit/2bc6f4d1a030661b3242244cd5f90260f4293620  
  
-> Assertion is changed to allow empty self-moving, which is typical when self swap is made through std::swap.  
-> priv_swap allows explicit self-swapping.  
  
Let me know if this change avoids assertions in your use case.

---

## Comment 3

> Username: Timmmm  
> Created_at: 2018-11-12 12:11:00 UTC  
> Url: https://github.com/boostorg/container/pull/84#issuecomment-437856882  

This might fix `std::swap(x, x)` however I think you misunderstood the bug report - this comment is wrong:  
  
       //x.size() == 0 is allowed for buggy std libraries.  
  
This bug report is *not* about buggy standard libraries. This is a bug in boost that is exposed by an unusual (but perfectly allowed) standard library. The following *should* work, but I don't think it will with your change.  
  
```  
boost::container::vector<int> x;  
x.push_back(5);  
x = std::move(x);  
```  
  
Hope that's clear!

---

## Comment 4

> Username: igaztanaga  
> Created_at: 2018-11-12 13:36:23 UTC  
> Url: https://github.com/boostorg/container/pull/84#issuecomment-437882481  

I was about to reply that self-movement need not to be supported at all, but this seems to have changed in C++17 (where MoveAssignable requirement has a note about self-assignment), but in C++11 any self move-assignment is undefined behavior. This change is reflected also in a SO question and Howard Hinnant's reply and update:  
  
https://stackoverflow.com/questions/13129031/on-implementing-stdswap-in-terms-of-move-assignment-and-move-constructor  
  
Boost.Container implemented C++11 guarantees, maybe it's time to update to C++17 requirements.

---

## Comment 5

> Username: igaztanaga  
> Created_at: 2018-11-12 13:42:38 UTC  
> Url: https://github.com/boostorg/container/pull/84#issuecomment-437884860  

Opened #88 for a general review of self move assignments.

---

## Comment 6

> Username: igaztanaga  
> Created_at: 2019-04-30 16:07:28 UTC  
> Url: https://github.com/boostorg/container/pull/84#issuecomment-488013645  

Closing this as #88 was fixed allowing vector to assign to itself. Thanks for the report.

---
