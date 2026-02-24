# #40 - same_fringe.cpp does not compare fringe [Closed]

> Username: badfd  
> Created at: 2019-05-21 11:41:37 UTC  
> Updated at: 2020-04-24 09:07:22 UTC  
> Closed at: 2020-04-24 09:07:22 UTC  
> Url: https://github.com/boostorg/coroutine/issues/40  

Dale Schumacher in ["Same Fringe" Revisited](http://www.dalnefre.com/wp/2017/01/same-fringe-revisited/) says that the fringe of a tree "... is simply the sequence of leaves reading from left to right."  
  
The coroutine example, **same_fringe.cpp**, includes non-leaf values amongst its comparisons. Perhaps the example could be renamed, or reworked to behave as expected.

---

## Comment 1

> Username: olk  
> Created at: 2020-04-24 09:07:22 UTC  
> Url: https://github.com/boostorg/coroutine/issues/40#issuecomment-618896518  

example is good as it is to get an idea how to use coroutine
