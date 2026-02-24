# #54 push_back: added support for not-copyable but moveable value-types li… [Merged]

> Username: cwecht  
> Created at: 2017-09-01 16:06:25 UTC  
> Updated at: 2018-04-24 15:44:53 UTC  
> Merged at: 2017-11-15 19:51:01 UTC  
> Closed at: 2017-11-15 19:51:01 UTC  
> Url: https://github.com/boostorg/range/pull/54  

…ke e.g. unique_ptr

---

## Comment 1

> Username: cwecht  
> Created_at: 2017-12-04 13:08:38 UTC  
> Url: https://github.com/boostorg/range/pull/54#issuecomment-348957415  

I disabled the moving-implementation in here: https://github.com/cwecht/range/tree/fix_push_back  
@pdimov could you run the test on this one?

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-12-04 13:15:59 UTC  
> Url: https://github.com/boostorg/range/pull/54#issuecomment-348959047  

No, but if you make a pull request here, Travis will run on it automatically.  
  
In principle you should also be able to enable Travis on your fork, but for some reason https://travis-ci.org/cwecht/range says "not found". You probably need to do something to give Travis rights to see your repos. https://docs.travis-ci.com/user/getting-started/

---
