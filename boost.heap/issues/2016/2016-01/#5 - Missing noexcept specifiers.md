# #5 - Missing noexcept specifiers [Closed]

> Username: apolukhin  
> Created at: 2016-01-16 11:08:05 UTC  
> Updated at: 2016-01-17 11:20:35 UTC  
> Closed at: 2016-01-17 11:20:35 UTC  
> Url: https://github.com/boostorg/heap/issues/5  

Move constructors and move assignments of heaps do not throw exceptions, but are not marked with `noexcept`. This leads to regressions when heaps are used in `std::vector`, or when heap is a member of a class with default move constructors/assignments.  
  
Please mark all the nonthrowing methods with `BOOST_NOEXCEPT`, or at least mark move constructors/assignments.

---

## Comment 1

> Username: timblechmann  
> Created at: 2016-01-16 14:55:32 UTC  
> Url: https://github.com/boostorg/heap/issues/5#issuecomment-172213500  

boost.heap is basically in maintenance mode for me atm, but i'm more than happy to review and merge pull requests!

---

## Comment 2

> Username: apolukhin  
> Created at: 2016-01-17 10:41:37 UTC  
> Url: https://github.com/boostorg/heap/issues/5#issuecomment-172311211  

OK, I'll try to make it soon.

---

## Comment 3

> Username: timblechmann  
> Created at: 2016-01-17 11:20:35 UTC  
> Url: https://github.com/boostorg/heap/issues/5#issuecomment-172315026  

thnx for the PR
