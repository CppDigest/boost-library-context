# #347 fuzz different memory allocation strategies [Closed]

> Username: pauldreik  
> Created at: 2020-09-16 08:11:07 UTC  
> Updated at: 2020-09-16 16:53:34 UTC  
> Closed at: 2020-09-16 16:51:17 UTC  
> Url: https://github.com/boostorg/json/pull/347  

This sets the BOOST_JSON_STACK_BUFFER_SIZE macro so interesting behaviour happens also for small inputs.  
  
Otherwise bugs may go undetected because fuzzing in general wants small inputs and may never reach an interesting size in a reasonable time.  
  
It also tries different memory handling strategies, as proposed by Vinnie on slack.   
  
I ran this for a few hours locally and it did not find anything on the current develop branch.  
  
Closes #333

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-09-16 09:15:53 UTC  
> Url: https://github.com/boostorg/json/pull/347#issuecomment-693281711  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/347/pullrequest-condensed-cc86279.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/347/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/347/pullrequest.html)

---
