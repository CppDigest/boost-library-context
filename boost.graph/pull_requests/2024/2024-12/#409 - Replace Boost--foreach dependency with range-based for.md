# #409 Replace Boost::foreach dependency with range-based for [Closed]

> Username: georgthegreat  
> Created at: 2024-12-21 10:59:17 UTC  
> Updated at: 2025-01-02 14:54:09 UTC  
> Closed at: 2025-01-02 14:52:31 UTC  
> Url: https://github.com/boostorg/graph/pull/409  

The library is marked as C++14, hence it should be safe to replace the dependency with native syntax.  
  
`boost::graph::vertices` returns `std::pair` which is not compatible with range-based for.  
As changing this behavior would result in API break, only public part was changed and the dependency still remains.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2024-12-21 21:47:47 UTC  
> Url: https://github.com/boostorg/graph/pull/409#issuecomment-2558248999  

Thanks!

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2024-12-21 21:50:08 UTC  
> Url: https://github.com/boostorg/graph/pull/409#issuecomment-2558249417  

If there are any build failures, please investigate.

---

## Comment 3

> Username: georgthegreat  
> Created_at: 2024-12-22 16:13:43 UTC  
> Url: https://github.com/boostorg/graph/pull/409#issuecomment-2558506312  

@jeremy-murphy, I had to reduce the amount of changes as it turns out that range-based-for is incompatible with vertices() method (it returns std::pair which is not a range in c++ terms, yet is accepted by BOOST_FOREACH).  
  
So, publicpart is rewritten and boost/foreach is no longer included from neither `include/` nor `src/` directories.  
  
Can we merge such (partial) improvement?

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2025-01-01 22:30:59 UTC  
> Url: https://github.com/boostorg/graph/pull/409#issuecomment-2567166645  

Whilst I think it's good to get rid of macros like `BOOST_FOREACH`, what I think is even better is to use algorithms in preference to `for ()` loops. And it turns out that the implementation in Boost.Ranges _does_ support `boost::pair` (and I _presume_ `std::pair`) as a range. However, I don't want you to go overboard with refactoring right away, as many of these loops have large bodies, so I recommend picking the low-hanging fruit, the stuff that looks obviously like a `find_if`, etc.

---

## Comment 5

> Username: georgthegreat  
> Created_at: 2025-01-02 14:54:06 UTC  
> Url: https://github.com/boostorg/graph/pull/409#issuecomment-2567896380  

range-based for is much clearer to me compared to huge functional-style algorithms.  
  
As I have failed to remove this dependency, I am closing this PR.

---
