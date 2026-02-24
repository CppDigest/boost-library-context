# #1091 raise priority of optional and variant above sequence [Merged]

> Username: grisumbras  
> Created at: 2025-07-02 10:15:39 UTC  
> Updated at: 2025-07-02 20:38:04 UTC  
> Merged at: 2025-07-02 20:38:01 UTC  
> Closed at: 2025-07-02 20:38:01 UTC  
> Url: https://github.com/boostorg/json/pull/1091  

Fix #1050.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-07-02 10:24:58 UTC  
> Url: https://github.com/boostorg/json/pull/1091#issuecomment-3027313237  

An automated preview of the documentation is available at [https://1091.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1091.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2025-07-02 11:27:21 UTC  
> Url: https://github.com/boostorg/json/pull/1091#issuecomment-3027512562  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1091/pullrequest-condensed-9146a6f.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1091/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1091/pullrequest.html)

---

## Comment 3

> Username: brevzin  
> Created_at: 2025-07-02 13:56:18 UTC  
> Url: https://github.com/boostorg/json/pull/1091#issuecomment-3027983694  

I don't think this is the right solution. It does directly solve this narrow problem, but it still does the wrong thing for [this example](https://github.com/boostorg/json/issues/1050#issuecomment-2397471194)   
  
I would still argue for specializing a [conversion kind](https://github.com/boostorg/json/issues/1050#issuecomment-2402422542) and having the automatic detection of kind fail if the type matches more than one category (because are you _sure_ you know which one the user meant?)

---

## Comment 4

> Username: grisumbras  
> Created_at: 2025-07-02 15:21:27 UTC  
> Url: https://github.com/boostorg/json/pull/1091#issuecomment-3028280476  

> I would still argue for specializing a https://github.com/boostorg/json/issues/1050#issuecomment-2402422542 and having the automatic detection of kind fail if the type matches more than one category (because are you sure you know which one the user meant?)  
  
I have another change that will add the ability to directly specialize the category (and deprecate the current category traits). But I don't think that default categorization should fail when several categories are matched. That would turn off default categorisation for `std::optional` and `std::array`. I think, it's okay to guess incorrectly if it's easily fixed by the user with one template specialisation. Looking back at filed issues I had, nobody (well, except for you in #1050)  has ever complained about a wrong categorization, but people have complained about things not getting categorized when they could have been.

---
