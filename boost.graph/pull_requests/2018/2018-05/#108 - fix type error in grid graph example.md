# #108 fix type error in grid graph example [Merged]

> Username: JohnCoconut  
> Created at: 2018-05-15 04:05:29 UTC  
> Updated at: 2018-10-13 08:54:15 UTC  
> Merged at: 2018-10-13 07:38:03 UTC  
> Closed at: 2018-10-13 07:38:03 UTC  
> Url: https://github.com/boostorg/graph/pull/108  

Won't compile using `boost::array<int, 2>` as grid graph constructor arguments.  
  
Change to `boost::array<std::size_t, 2>`.

---

## Comment 1

> Username: anadon  
> Created_at: 2018-08-31 17:34:47 UTC  
> Url: https://github.com/boostorg/graph/pull/108#issuecomment-417737612  

I'm helping out with the PR backlog. Looks like you have a bugfix, no test, and no new examples will be needed.  I would like you to add a new test that works with your new fix and does not work on previous versions.  This is to let you know and help me prioritize PR's.

---

## Comment 2

> Username: anadon  
> Created_at: 2018-10-12 19:53:16 UTC  
> Url: https://github.com/boostorg/graph/pull/108#issuecomment-429443961  

@jzmaddock Here's a quick doc merge.

---
