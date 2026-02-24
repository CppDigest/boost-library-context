# #220 Account for sleep time in timing tests [Merged]

> Username: thughes  
> Created at: 2018-04-27 20:24:38 UTC  
> Updated at: 2018-04-28 15:47:38 UTC  
> Merged at: 2018-04-28 15:47:38 UTC  
> Closed at: 2018-04-28 15:47:38 UTC  
> Url: https://github.com/boostorg/thread/pull/220  

As discussed in https://github.com/boostorg/thread/pull/213#issuecomment-380600387  
  
Note that the one type of timing test I couldn't apply this approach to are those that involve condition variables, since there's no way to account for oversleeping (i.e., longer than requested) in `pthread_cond_timedwait`. That is why the mac builds still use a slightly higher timeout. See this [build output for an example](https://circleci.com/gh/thughes/thread/483).  
  
Overall this makes the tests much more reliable on CircleCI. I think it will help other people making PRs so they don't wonder why the unit tests randomly fail in tests unrelated to their changes.

---
