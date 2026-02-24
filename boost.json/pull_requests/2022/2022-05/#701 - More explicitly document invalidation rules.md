# #701 More explicitly document invalidation rules [Closed]

> Username: sehe  
> Created at: 2022-05-07 15:53:26 UTC  
> Updated at: 2022-05-15 15:12:36 UTC  
> Closed at: 2022-05-14 17:20:23 UTC  
> Url: https://github.com/boostorg/json/pull/701  

The reference/iterator invalidation rules can surprise people who expect  
associative containers to be node-based, similar to `std::[unordered_]map`.  
  
This calls it out more visibly to avoid surprises.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-05-07 16:01:26 UTC  
> Url: https://github.com/boostorg/json/pull/701#issuecomment-1120232987  

An automated preview of the documentation is available at [https://701.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://701.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: sehe  
> Created_at: 2022-05-07 16:14:17 UTC  
> Updated_at: 2022-05-07 16:19:12 UTC  
> Url: https://github.com/boostorg/json/pull/701#issuecomment-1120234959  

I got the snippet reference wrong after all. Sigh. Also, it looks weird because the callout box is hiding the snippet box. Perhaps it's better to put the callout at the bottom, separate from the snippet?  
  
Will update so that we have something tangible to compare. Snapshot of current preview ![image](https://user-images.githubusercontent.com/324097/167262922-80161dea-5719-4220-910e-e5b06322582e.png)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-05-07 16:26:28 UTC  
> Url: https://github.com/boostorg/json/pull/701#issuecomment-1120236626  

An automated preview of the documentation is available at [https://701.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://701.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 4

> Username: sehe  
> Created_at: 2022-05-07 16:29:58 UTC  
> Url: https://github.com/boostorg/json/pull/701#issuecomment-1120237092  

I think this looks better now.

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-05-07 17:34:31 UTC  
> Url: https://github.com/boostorg/json/pull/701#issuecomment-1120247331  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/701/pullrequest-condensed-081d240.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/701/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/701/pullrequest.html)

---

## Comment 6

> Username: grisumbras  
> Created_at: 2022-05-14 17:20:23 UTC  
> Url: https://github.com/boostorg/json/pull/701#issuecomment-1126779358  

Merged via 98d37f93706b50fd057d14bd241a2bfbcfec1527

---
