# #385 Add support for weighted fraction accumulator [Open]

> Username: ryanelandt  
> Created at: 2023-06-05 04:33:54 UTC  
> Updated at: 2023-06-20 15:57:35 UTC  
> Url: https://github.com/boostorg/histogram/pull/385  

This PR extends `fraction`to weighted samples to address issue #367.  
  
I took the approach of creating a new `weighted_fraction` class. This class is a composition of two classes: `fraction` and a new internal class `sum_of_weights_squared`. I explain the thought process behind these choices.  
  
The classes `sum`, `mean`, and `fraction` are non-weighted. Each of these classes could almost function as a weighted class, but requires one additional piece of information: the sum of the weights squared, that is to say $\sum (w^2)$. So, I turned this piece of information into a class `sum_of_weights_squared`. I created the class `weighted_fraction` by putting `fraction` and `sum_of_weights_squared` together.  
  
The code passes all unit tests (locally), but has `Wpedantic` warnings because `wilson_solve` uses a designated initializer (i.e., `wilson_solve({.n_eff=n_eff, .p_hat=p_hat, .correction=correction})`). There's likely a good way to avoid getting this warning, while also enforcing input meaning, but I'm missing it.

---

## Comment 1

> Username: HDembinski  
> Created_at: 2023-06-05 06:51:58 UTC  
> Url: https://github.com/boostorg/histogram/pull/385#issuecomment-1576146026  

Thanks for this substantial contribution, I will review it next weekend. I think you can replace the initializer to remove the warning.

---

## Review 2 [Commented]

> Username: HDembinski  
> Created_at: 2023-06-06 10:12:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/385#pullrequestreview-1464824720  

📁 include/boost/histogram/accumulators/fraction.hpp

```diff
  39 |                                               value_type, double>::type;
  39 |-   using interval_type = typename utility::wilson_interval<real_type>::interval_type;
  40 |+   using score_type = typename utility::wilson_interval<real_type>;
```

> Username: HDembinski  
> Created_at: 2023-06-06 10:12:18 UTC  
> Url: https://github.com/boostorg/histogram/pull/385#discussion_r1219352536  

score_type should not appear here, because it is not a type that needs to be publicly visible.

> Username: ryanelandt  
> Created_at: 2023-06-06 15:38:08 UTC  
> Url: https://github.com/boostorg/histogram/pull/385#discussion_r1219880197  

This issue should be addressed now. Please take a look.


---

## Comment 3

> Username: ryanelandt  
> Created_at: 2023-06-12 14:11:09 UTC  
> Url: https://github.com/boostorg/histogram/pull/385#issuecomment-1587424231  

The failing check is due to the job never starting and eventually timing out.

---

## Comment 4

> Username: HDembinski  
> Created_at: 2023-06-12 15:32:17 UTC  
> Url: https://github.com/boostorg/histogram/pull/385#issuecomment-1587577650  

This sometimes happens. I restarted the job. If it continues to fail, we can ignore it. Checking compatibility with an ancient compiler becomes less and less useful as time progresses.

---

## Comment 5

> Username: ryanelandt  
> Created_at: 2023-06-19 19:45:34 UTC  
> Url: https://github.com/boostorg/histogram/pull/385#issuecomment-1597676977  

Please let me know if there are any changes I can make to this.

---

## Comment 6

> Username: HDembinski  
> Created_at: 2023-06-20 07:51:59 UTC  
> Url: https://github.com/boostorg/histogram/pull/385#issuecomment-1598290779  

I apologize for being slow with responding. I appreciate your enthusiasm to contribute to Boost.Histogram. I need some time to carefully think about the APIs, because in Boost we have very high standards in regards to stability.  
  
It would be a good idea to start a new subfolder called `experimental` which contains new components such as this. Components in `experimental` are not promiosed to have a stable API. This would lower the bar for adoption.

---

## Comment 7

> Username: ryanelandt  
> Created_at: 2023-06-20 15:57:35 UTC  
> Url: https://github.com/boostorg/histogram/pull/385#issuecomment-1599075691  

I moved the `weighted_fraction` class to an `experimental` folder. I also made `weighted_fraction` a friend of `fraction` which helps minimize changes to the public API. Let me know if there are any other changes I can make.

---
