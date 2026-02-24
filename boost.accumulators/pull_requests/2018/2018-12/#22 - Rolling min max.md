# #22 Rolling min max [Open]

> Username: qchateau  
> Created at: 2018-12-21 01:11:47 UTC  
> Updated at: 2019-09-28 18:18:03 UTC  
> Url: https://github.com/boostorg/accumulators/pull/22  

Rolling min and max with insertion complexity O(logN), where N is the window size, and extraction complexity O(1).  
  
Is this fit for a merge into the main library ?  
  
To be fair, I don't even know if this is the right place to submit a patch, but I could not find any information on how to contribute to accumulators. I hope someone can help.

---

## Review 1 [Commented]

> Username: yuvalif  
> Created_at: 2018-12-24 07:02:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/accumulators/pull/22#pullrequestreview-187652750  

📁 doc/accumulators.qbk

```diff
2128 |+ [section:rolling_min rolling_min]
2129 |+ 
2130 |+ The rolling sum is the sum of the last /N/ samples.
```

> Username: yuvalif  
> Created_at: 2018-12-24 07:02:34 UTC  
> Updated_at: 2019-09-28 18:18:03 UTC  
> Url: https://github.com/boostorg/accumulators/pull/22#discussion_r243792684  

should be "min" not "sum"


---

## Review 2 [Commented]

> Username: yuvalif  
> Created_at: 2018-12-24 07:03:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/accumulators/pull/22#pullrequestreview-187652815  

📁 doc/accumulators.qbk

```diff
2080 |+ [section:rolling_max rolling_max]
2081 |+ 
2082 |+ The rolling sum is the sum of the last /N/ samples.
```

> Username: yuvalif  
> Created_at: 2018-12-24 07:03:30 UTC  
> Updated_at: 2019-09-28 18:18:03 UTC  
> Url: https://github.com/boostorg/accumulators/pull/22#discussion_r243792738  

should be "max" not "sum"


---

## Comment 3

> Username: yuvalif  
> Created_at: 2018-12-24 07:07:52 UTC  
> Url: https://github.com/boostorg/accumulators/pull/22#issuecomment-449695206  

@Tytan what if someone just wants min/max but without sorted rolling window? in this case insertion complexity should be O(1). only dependency should be of rolling window, not the sorted one.

---

## Comment 4

> Username: qchateau  
> Created_at: 2018-12-24 08:22:38 UTC  
> Url: https://github.com/boostorg/accumulators/pull/22#issuecomment-449701813  

I will fix the documentation. What would you say is the quickest way of generating a human readable version of the doc ?  
  
I sure can implement a lazy and immediate version of the rolling min and max if you deem the complexity acceptable. Anyway, you are right, people should be allowed to have the choice.  
  
In this case what would you say should be the default implementation ? The lazy one would not mess with insertion cost, but I think people expect extraction to be fast.

---

## Comment 5

> Username: yuvalif  
> Created_at: 2018-12-24 08:48:38 UTC  
> Url: https://github.com/boostorg/accumulators/pull/22#issuecomment-449704369  

> I will fix the documentation. What would you say is the quickest way of generating a human readable version of the doc ?  
  
I have no idea... maybe ask on the mailing list?  
  
> I sure can implement a lazy and immediate version of the rolling min and max if you deem the complexity acceptable. Anyway, you are right, people should be allowed to have the choice.  
> In this case what would you say should be the default implementation ? The lazy one would not mess with insertion cost, but I think people expect extraction to be fast.  
  
In general insertion cost is more important than extraction (assuming there are more insertions than extractions), but probably best to look at other accumulators where both options exist.   
Would be even nicer (though I'm not sure how to do that...) if you can collapse the lazy implementation to the regular one if you need the ordered window anyway (e.g. combining lazy min with regular max)

---

## Comment 6

> Username: qchateau  
> Created_at: 2018-12-24 10:48:26 UTC  
> Url: https://github.com/boostorg/accumulators/pull/22#issuecomment-449718196  

I had to split the declaration of rolling_window and rolling_window_plus1 in rolling_window.hpp because GCC was complaining that the extractor of rolling_window used the extractor of rolling_window_plus1 before it was declared.  
  
I added a lazy/immediate implementation for rolling min and max. The default being the lazy one.  
  
I think addition of a mecanism to chose the immediate implementation instead of the lazy one if the sorted_rolling_window is used as a dependency by another feature in the set can be the subject of a further PR. I may work on this later on.

---
