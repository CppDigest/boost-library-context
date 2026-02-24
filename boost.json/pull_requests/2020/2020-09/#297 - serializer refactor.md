# #297 serializer refactor [Merged]

> Username: vinniefalco  
> Created at: 2020-09-07 17:35:55 UTC  
> Updated at: 2020-09-07 18:43:14 UTC  
> Merged at: 2020-09-07 18:43:14 UTC  
> Closed at: 2020-09-07 18:43:14 UTC  
> Url: https://github.com/boostorg/json/pull/297  

* serialize() replaces to_string()  
    * serialize.hpp replaces to_string.hpp  
    * serializer works on all types  
    * serialize, operator<< work on all types  
    * reset() takes pointer arguments  
    * resetting constructor removed

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-09-07 17:41:27 UTC  
> Url: https://github.com/boostorg/json/pull/297#issuecomment-688452474  

An automated preview of the documentation is available at [http://297.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://297.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: sdkrystian  
> Created_at: 2020-09-07 18:34:43 UTC  
> Url: https://github.com/boostorg/json/pull/297#issuecomment-688468020  

Looks good save for the fuzzer failure. Maybe we should add support for integers?

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2020-09-07 18:40:25 UTC  
> Url: https://github.com/boostorg/json/pull/297#issuecomment-688469850  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/297/pullrequest-condensed.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/297/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/297/pullrequest.html)

---
