# #678 s390x architecture drone jobs [Closed]

> Username: sdarwin  
> Created at: 2022-02-10 13:47:45 UTC  
> Updated at: 2022-02-11 14:42:59 UTC  
> Closed at: 2022-02-11 14:42:59 UTC  
> Url: https://github.com/boostorg/json/pull/678  

s390x jobs are running at cloud.ibm.com on their new VPC Infrastructure, Tokyo region.   
Drone doesn't include an IBM autoscaler currently, instances are running as provisioned virtual servers.  
The json clang-12 job completes in 8 minutes. The json gcc job takes more than 1 hour.  I've commented out the gcc job for now. You could test/debug in a feature branch or fork.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-02-10 14:59:28 UTC  
> Url: https://github.com/boostorg/json/pull/678#issuecomment-1035021085  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/678/pullrequest-condensed-2373b82.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/678/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/678/pullrequest.html)

---

## Comment 2

> Username: grisumbras  
> Created_at: 2022-02-11 14:28:06 UTC  
> Url: https://github.com/boostorg/json/pull/678#issuecomment-1036267685  

Merged via 407fbb809bec96d78dd2a5e8e336356dba0445a9

---
