# #938 test: add and use test settings [Merged]

> Username: barendgehrels  
> Created at: 2021-11-17 14:08:47 UTC  
> Updated at: 2021-11-24 09:32:29 UTC  
> Merged at: 2021-11-24 09:32:25 UTC  
> Closed at: 2021-11-24 09:32:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/938  

This  
* excludes cases failing for rescaling  
* removes duplicate testcases (all was already tested in all directions)  
* adds settings to explicitly just skip some of the cases  
* fixes svg and other small changes  
  
There are still some cases failing without rescaling, to be investigated further

---

## Review 1 [Commented]

> Username: vissarion  
> Created_at: 2021-11-19 13:38:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/938#pullrequestreview-811213772  

📁 test/algorithms/set_operations/difference/difference_linear_linear.cpp

```diff
 271 |          "lldf16");
 272 | 
 273 |-     tester::apply
```

> Username: vissarion  
> Created_at: 2021-11-19 13:38:28 UTC  
> Updated_at: 2021-11-19 13:38:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/938#discussion_r753196662  

I understand that all the reverse tests `-r` are removed as duplicates and the rest are disactivated for now and fixed in the future. Are the `-r` tests failing now too? Are they going to be fixed in the future as well?

> Username: barendgehrels  
> Created_at: 2021-11-20 09:21:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/938#discussion_r753655020  

- the `-r` cases are indeed removed as duplicate  
- some of the `-r` cases are now failing, and only they are disabled  
- yes, this was the first step to see what's going wrong there and I will continue next Wednesday  
- so this PR is a first step - it was the last step in letting all tests pass, without rescaling, in set_operations and buffer

> Username: awulkiew  
> Created_at: 2021-11-23 12:44:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/938#discussion_r755087423  

I don't understand what is the rationale behind removing them? These are not duplicates since the inputs are different. The fact that some of them fails now is a good reason why they should stay. It's because they might fail in the future after changing something else. This is what tests are for, not to pass but to fail if something is wrong.

> Username: vissarion  
> Created_at: 2021-11-23 13:52:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/938#discussion_r755142816  

what I was understood is that the tester function will be responsible to test the "reverse" case thus in the scenario the `-r` cases are duplicates. But I strongly agree that the reverse cases should be always tested.

> Username: awulkiew  
> Created_at: 2021-11-23 14:24:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/938#discussion_r755173861  

@vissarion Ok thanks. So they are not removed but instead various `settings` like `test_normal_reverse` are used inside.

> Username: barendgehrels  
> Created_at: 2021-11-24 09:32:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/938#discussion_r755852846  

Sure, conform description  
  
The reverse cases where already tested inside. So they were always duplicate. But now they are configurable.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2021-11-20 09:23:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/938#pullrequestreview-811814941  

📁 test/algorithms/set_operations/difference/difference_linear_linear.cpp

```diff
 517 |+         // Cases failing without rescaling when first linestring is reversed
 518 |+         settings.test_reverse_normal = false;
 519 |+         settings.test_reverse_reverse = false;
```

> Username: barendgehrels  
> Created_at: 2021-11-20 09:23:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/938#discussion_r753655125  

@vissarion for example you can see here that only two of the cases are excluded from the test, and only for if rescaling is not used. So in the default case, everything is still tested as it was before (now without duplicates)


---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2021-11-23 11:11:35 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/938#pullrequestreview-813519241  

Thanks for the explanations.

---
