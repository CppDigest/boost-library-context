# #342 Use loglog scale for plotting performance data [Merged]

> Username: ddemidov  
> Created at: 2014-12-15 13:26:21 UTC  
> Updated at: 2014-12-15 20:23:33 UTC  
> Merged at: 2014-12-15 19:21:35 UTC  
> Closed at: 2014-12-15 19:21:35 UTC  
> Url: https://github.com/boostorg/compute/pull/342  

This makes performance comparison easier, especially for smaller sizes.  
  
An example:  
  
![sort_perf](https://cloud.githubusercontent.com/assets/270503/5436349/24795482-8476-11e4-90be-c5faac6b68b3.png)  
  
Here one may clearly see that there is no point in using GPUs when there are less than 1e5 elements to sort. It is also clear that all backends have similar algorithmic complexity for larger problems.  
  
Compare it to the current plot:  
![sort_time_plot](https://cloud.githubusercontent.com/assets/270503/5436421/17aa7fa0-8477-11e4-8d35-ad311b5f2e5d.png)

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-12-15 13:50:35 UTC  
> Url: https://github.com/boostorg/compute/pull/342#issuecomment-66996139  

[![Coverage Status](https://coveralls.io/builds/1612789/badge)](https://coveralls.io/builds/1612789)  
  
Coverage remained the same when pulling **292e5136eda20244166decd488489f17e2254be9 on ddemidov:master** into **57eec36d20e122a496d3fbfddf8bf4ad8d6f4a4f on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-12-15 19:22:15 UTC  
> Url: https://github.com/boostorg/compute/pull/342#issuecomment-67049192  

This looks a lot clearer/better. Thanks!

---

## Comment 3

> Username: ddemidov  
> Created_at: 2014-12-15 19:35:53 UTC  
> Url: https://github.com/boostorg/compute/pull/342#issuecomment-67051391  

It could also be a good idea to provide Thrust results on [Performance](http://kylelutz.github.io/compute/boost_compute/performance.html) page. I can provide the plots if you are interested.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-12-15 19:40:38 UTC  
> Url: https://github.com/boostorg/compute/pull/342#issuecomment-67052126  

That would be great! The only NVIDIA hardware I have locally is a very old NVS 4200M in my laptop (currently all the plots are made using an AMD HD 7970). I think you may be able to add them to a pull-request against the `gh-pages` branch. Otherwise I should be able to upload them later tonight.

---

## Comment 5

> Username: ddemidov  
> Created_at: 2014-12-15 19:50:27 UTC  
> Url: https://github.com/boostorg/compute/pull/342#issuecomment-67053747  

The page header (  
  
```  
The following tests were run with an AMD HD 7970 "Tahiti" GPU on a system with an Intel i7-4771 3.50GHz CPU.  
```  
  
) should also be changed to my hardware, and I don't know how to generate the html from quickbook files. If that is easy enough, I could do the PR.

---

## Comment 6

> Username: ddemidov  
> Created_at: 2014-12-15 20:23:33 UTC  
> Url: https://github.com/boostorg/compute/pull/342#issuecomment-67058888  

Created #346 and #347.

---
