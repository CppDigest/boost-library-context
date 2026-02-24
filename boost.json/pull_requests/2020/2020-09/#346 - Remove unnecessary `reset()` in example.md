# #346 Remove unnecessary `reset()` in example [Closed]

> Username: akrzemi1  
> Created at: 2020-09-16 06:31:33 UTC  
> Updated at: 2020-09-16 16:51:17 UTC  
> Closed at: 2020-09-16 16:51:17 UTC  
> Url: https://github.com/boostorg/json/pull/346  

The call to `parser::reset` just after default construction is, as I understand, redundant.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-09-16 07:36:19 UTC  
> Url: https://github.com/boostorg/json/pull/346#issuecomment-693231273  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/346/pullrequest-condensed-546c69f.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/346/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/346/pullrequest.html)

---
