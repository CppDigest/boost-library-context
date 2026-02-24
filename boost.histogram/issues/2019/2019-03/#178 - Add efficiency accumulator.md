# #178 - Add efficiency accumulator [Closed]

> Username: HDembinski  
> Created at: 2019-03-27 08:22:19 UTC  
> Updated at: 2022-11-06 09:31:54 UTC  
> Closed at: 2022-11-06 09:30:49 UTC  
> Url: https://github.com/boostorg/histogram/issues/178  

Histograms are often used to compute efficiencies in HEP. This is best done with a dedicated accumulator, to avoid filling two separate histograms. If one uses two histograms, the bin lookup is done twice instead of once.  
  
Code could look like this:  
  
```c++  
auto h = make_histogram_with(dense_storage<accumulator::efficiency<>>(), axis::regular<>(10, 0, 1));  
  
h(0.1, sample(false)); // didn't pass  
h(0.5, sample(true)); // did pass  
  
h.at(0).value(); // gives the efficiency in that bin  
h.at(0).variance(); // gives variance estimate for that bin  
auto interval = h.at(0).confidence_interval(); // gives the confidence interval  
```  
  
The `confidence_interval()` method could have optional parameters for the desired coverage and for the way on how to compute the confidence interval, by which method (should default to Wilson). However, it is better to pass these options in statically as a template parameter in the accumulator, because you never request Clopper-Pearson intervals for some bins and Wilson intervals for others. If you want and need to switch the interval computation method, you should only have to change your code in the place where the histogram is created. Not in every call of the `confidence_interval()` method.  
  
User guide on writing custom accumulators:  
https://www.boost.org/doc/libs/develop/libs/histogram/doc/html/histogram/guide.html#histogram.guide.expert.user_defined_accumulators  
  
If anyone wants to work on this, I am happy to review and guide you. It is not a difficult task, but you will learn a lot about the nitty-gritty details of writing a high-quality library implementation. It requires patience and a willingness to learn and receive constructive criticism.

---

## Comment 1

> Username: psbro  
> Created at: 2020-12-08 02:21:35 UTC  
> Url: https://github.com/boostorg/histogram/issues/178#issuecomment-740317169  

i want to contribute to this issue.......would you please explain it more to me??

---

## Comment 2

> Username: HDembinski  
> Created at: 2020-12-08 16:50:02 UTC  
> Url: https://github.com/boostorg/histogram/issues/178#issuecomment-740755808  

Hi @psbro, thank you for your interest in contributing. You can start by reading the [user guide](https://www.boost.org/doc/libs/develop/libs/histogram/doc/html/histogram/guide.html) on how to make a simple accumulator for boost histogram. Then have a look at the current accumulators in the library (include/boost/histogram/accumulators) to see how they are implemented. The new accumulator is similar to the `mean` accumulator.

---

## Comment 3

> Username: HDembinski  
> Created at: 2020-12-08 16:52:14 UTC  
> Url: https://github.com/boostorg/histogram/issues/178#issuecomment-740757291  

Make an accumulator with two counters. Increment the first counter if the sample value passed to the accumulator is `true`, and increment the second counter if it is `false`. Let me know when you are there, then we discuss next steps.  
  
Tipp: You can play around with Boost Histogram online on Godbolt, for example.

---

## Comment 4

> Username: psbro  
> Created at: 2020-12-17 10:56:27 UTC  
> Url: https://github.com/boostorg/histogram/issues/178#issuecomment-747367114  

i cant get it excatly what you want to say....would you please elaboarte it

---

## Comment 5

> Username: ShriyanshiSrivastava  
> Created at: 2021-01-24 16:13:40 UTC  
> Url: https://github.com/boostorg/histogram/issues/178#issuecomment-766375046  

Hello !  
I would like to contribute to this issue:))

---

## Comment 6

> Username: HDembinski  
> Created at: 2021-11-15 08:39:03 UTC  
> Url: https://github.com/boostorg/histogram/issues/178#issuecomment-968655094  

@ShriyanshiSrivastava   
I am very sorry that I missed your comment, if you are still interested please go ahead with a draft, then make a pull request and we can discuss it.

---

## Comment 7

> Username: HDembinski  
> Created at: 2022-11-06 09:30:49 UTC  
> Updated at: 2022-11-06 09:31:54 UTC  
> Url: https://github.com/boostorg/histogram/issues/178#issuecomment-1304746466  

Implemented in #361
