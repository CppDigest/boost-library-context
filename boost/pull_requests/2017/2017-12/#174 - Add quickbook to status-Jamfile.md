# #174 Add quickbook to status/Jamfile [Merged]

> Username: pdimov  
> Created at: 2017-12-02 18:04:36 UTC  
> Updated at: 2017-12-17 15:00:18 UTC  
> Merged at: 2017-12-16 21:47:55 UTC  
> Closed at: 2017-12-16 21:47:55 UTC  
> Url: https://github.com/boostorg/boost/pull/174  



---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2017-12-17 13:07:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost/pull/174#pullrequestreview-83999871  

This had broken the whole tests suit. `--dump-tests`  now returns only one line `boost-test(RUN) "quickbook/xmlbase-1_6-fail" :`

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-12-17 13:37:07 UTC  
> Url: https://github.com/boostorg/boost/pull/174#issuecomment-352256212  

Yes, it seems that there's something in Quickbook's test Jamfile that breaks --dump-tests. I'm trying to figure out what.

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-12-17 13:44:05 UTC  
> Url: https://github.com/boostorg/boost/pull/174#issuecomment-352256684  

Pretty sure it's this thing  
  
```  
    modules.poke testing : .all-tests : \$\(all-tests\) $(t) ;  
```  
  
not yet positive on why it doesn't work and how it needs to be fixed. :-)

---

## Comment 4

> Username: Kojoley  
> Created_at: 2017-12-17 13:52:15 UTC  
> Url: https://github.com/boostorg/boost/pull/174#issuecomment-352257221  

Yes, I was looking at this too. Removing them fixes the problem.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2017-12-17 14:02:42 UTC  
> Url: https://github.com/boostorg/boost/pull/174#issuecomment-352257841  

`modules.poke testing : .all-tests : [ modules.peek testing : .all-tests ] $(t) ;` solves the problem.

---

## Comment 6

> Username: pdimov  
> Created_at: 2017-12-17 14:03:36 UTC  
> Url: https://github.com/boostorg/boost/pull/174#issuecomment-352257911  

I'm pretty sure that this needs to be something like  
  
```  
    local all-tests = [ modules.peek testing : .all-tests ] ;  
    all-tests += $(t) ;  
    modules.poke testing : .all-tests : $(all-tests) ;  
```  
  
What I'm not sure about though is whether the `QUICKBOOK_OUTPUT` tests need to be added to `.all-tests`.

---

## Comment 7

> Username: pdimov  
> Created_at: 2017-12-17 14:04:31 UTC  
> Url: https://github.com/boostorg/boost/pull/174#issuecomment-352257970  

Crossed the streams. :-)

---

## Comment 8

> Username: Kojoley  
> Created_at: 2017-12-17 14:24:50 UTC  
> Url: https://github.com/boostorg/boost/pull/174#issuecomment-352259245  

> What I'm not sure about though is whether the `QUICKBOOK_OUTPUT` tests need to be added to `.all-tests`.  
  
I guess no, `testing` does not add `RUN_OUTPUT` targets to `.all-tests`. It looks like `QUICKBOOK_OUTPUT`  is just a remake of `make` rule.

---

## Comment 9

> Username: pdimov  
> Created_at: 2017-12-17 15:00:17 UTC  
> Url: https://github.com/boostorg/boost/pull/174#issuecomment-352261579  

https://github.com/boostorg/quickbook/pull/3

---
