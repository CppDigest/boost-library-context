# #11 Fix undefined behavior in interval_bounds::reverse_right() [Merged]

> Username: avikivity  
> Created at: 2017-07-02 09:59:00 UTC  
> Updated at: 2017-11-22 00:14:54 UTC  
> Merged at: 2017-11-22 00:14:53 UTC  
> Closed at: 2017-11-22 00:14:54 UTC  
> Url: https://github.com/boostorg/icl/pull/11  

Found by gcc's ubsan. See commit log for details.

---

## Comment 1

> Username: avikivity  
> Created_at: 2017-07-02 10:03:19 UTC  
> Url: https://github.com/boostorg/icl/pull/11#issuecomment-312481988  

Only the last commit is mine, because I based my commit on master but github wants to pull into develop. Let me know if I should redo it based on develop, or retarget the pull request into master.

---

## Comment 2

> Username: duarten  
> Created_at: 2017-07-11 18:53:56 UTC  
> Url: https://github.com/boostorg/icl/pull/11#issuecomment-314538703  

+1

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-11-21 17:56:06 UTC  
> Url: https://github.com/boostorg/icl/pull/11#issuecomment-346108996  

Pull requests should be based on develop, yes.

---

## Comment 4

> Username: avikivity  
> Created_at: 2017-11-21 18:59:15 UTC  
> Url: https://github.com/boostorg/icl/pull/11#issuecomment-346126763  

@pdimov I rebased on top of develop and force-pushed.

---
