# #6 Add scatterv and gatherv collective operations [Merged]

> Username: juliohm  
> Created at: 2014-05-03 17:30:31 UTC  
> Updated at: 2014-07-12 21:21:23 UTC  
> Merged at: 2014-07-12 21:21:23 UTC  
> Closed at: 2014-07-12 21:21:23 UTC  
> Url: https://github.com/boostorg/mpi/pull/6  

This patch was originally developed for Boost 1.45.0. It's completely based on the existent `scatter.hpp` and `gather.hpp` headers.

---

## Comment 1

> Username: Belcourt  
> Created_at: 2014-05-03 19:59:29 UTC  
> Url: https://github.com/boostorg/mpi/pull/6#issuecomment-42114858  

As I mentioned in your other pull request, we'll need some tests, updated documentation, and, ideally, an couple of examples before we can merge this pull request.

---

## Comment 2

> Username: juliohm  
> Created_at: 2014-05-03 21:36:11 UTC  
> Url: https://github.com/boostorg/mpi/pull/6#issuecomment-42117068  

Sure @Belcourt, I'll find time to improve the patch.

---

## Comment 3

> Username: juliohm  
> Created_at: 2014-06-17 16:37:53 UTC  
> Url: https://github.com/boostorg/mpi/pull/6#issuecomment-46332454  

@Belcourt, could you please review this pull request? I've added tests and linked these two functions in the `secion:c_mapping` of the documentation as you requested. Please let me know if anything is missing.  
  
I've decided to not implement the Python bindings for `scatterv` and `gatherv` because they are somehow not "pythonic", and low-level in a sense that Python users don't like. My decision was also based on the existent [mpi4py](http://mpi4py.scipy.org/) package, but is open to discussion.

---

## Comment 4

> Username: Belcourt  
> Created_at: 2014-06-17 20:26:27 UTC  
> Url: https://github.com/boostorg/mpi/pull/6#issuecomment-46360447  

I'm on travel but will try to get to this by this weekend, sorry about the delay.

---

## Comment 5

> Username: juliohm  
> Created_at: 2014-07-11 16:32:13 UTC  
> Url: https://github.com/boostorg/mpi/pull/6#issuecomment-48752680  

@Belcourt, sorry bothering you, what is the status of this PR?

---
