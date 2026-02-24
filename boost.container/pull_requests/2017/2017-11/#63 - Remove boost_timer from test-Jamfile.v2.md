# #63 Remove boost_timer from test/Jamfile.v2 [Closed]

> Username: pdimov  
> Created at: 2017-11-02 16:46:39 UTC  
> Updated at: 2017-11-08 21:09:05 UTC  
> Closed at: 2017-11-07 14:17:28 UTC  
> Url: https://github.com/boostorg/container/pull/63  

`depinst.py` finds no references to Boost.Timer and doesn't install it, so I'm creating this PR to take it out and see if testing will succeed on Travis.

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-11-02 23:27:07 UTC  
> Url: https://github.com/boostorg/container/pull/63#issuecomment-341587655  

Tests pass for me locally with `/boost/timer//boost_timer` removed from the test Jamfile, and it fixes Travis. I'll leave the decision of whether to merge it to you.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2017-11-08 21:09:05 UTC  
> Url: https://github.com/boostorg/container/pull/63#issuecomment-342960326  

Thanks for the patch.

---
