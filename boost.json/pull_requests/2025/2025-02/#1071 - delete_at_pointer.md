# #1071 delete_at_pointer [Closed]

> Username: RoyBellingan  
> Created at: 2025-02-15 01:54:02 UTC  
> Updated at: 2026-01-06 06:14:07 UTC  
> Closed at: 2026-01-06 06:09:09 UTC  
> Url: https://github.com/boostorg/json/pull/1071  

Some provisional code to add support for the delete_at_pointer functionality.  
  
To test with   
  
https://github.com/RoyBellingan/boostDeleteAtPointer  
  
It should produce this  
  
```JSON  
Initial Json:  
[  
    {  
        "image" : {  
            "path" : "https://i.natgeofe.com/n/4f5aaece-3300-41a4-b2a8-ed2708a0a27c/domestic-dog_thumb_square.jpg?wp=1&w=170&h=170",  
            "size" : 9100  
        },  
        "comment" : [  
            {  
                "text" : "this is cool",  
                "timestamp" : 123456,  
                "likes" : [  
                    {  
                        "author" : "Coco",  
                        "timestamp" : 123  
                    },  
                    {  
                        "author" : "Izzy",  
                        "timestamp" : 456  
                    }  
                ]  
            }  
        ]  
    }  
]  
Removing invalid ptr /1/image   
past-the-end token not supported @ /1  
------  
Removing invalid ptr /0/image/invalid   
no referenced value @ /0/image/invalid  
------  
Removing /0/comment/0/text   
[  
    {  
        "image" : {  
            "path" : "https://i.natgeofe.com/n/4f5aaece-3300-41a4-b2a8-ed2708a0a27c/domestic-dog_thumb_square.jpg?wp=1&w=170&h=170",  
            "size" : 9100  
        },  
        "comment" : [  
            {  
                "likes" : [  
                    {  
                        "author" : "Coco",  
                        "timestamp" : 123  
                    },  
                    {  
                        "author" : "Izzy",  
                        "timestamp" : 456  
                    }  
                ],  
                "timestamp" : 123456  
            }  
        ]  
    }  
]  
  
------  
Removing /0/comment/0/likes/1   
[  
    {  
        "image" : {  
            "path" : "https://i.natgeofe.com/n/4f5aaece-3300-41a4-b2a8-ed2708a0a27c/domestic-dog_thumb_square.jpg?wp=1&w=170&h=170",  
            "size" : 9100  
        },  
        "comment" : [  
            {  
                "likes" : [  
                    {  
                        "author" : "Coco",  
                        "timestamp" : 123  
                    }  
                ],  
                "timestamp" : 123456  
            }  
        ]  
    }  
]  
  
------  
Removing /0/comment/0   
[  
    {  
        "image" : {  
            "path" : "https://i.natgeofe.com/n/4f5aaece-3300-41a4-b2a8-ed2708a0a27c/domestic-dog_thumb_square.jpg?wp=1&w=170&h=170",  
            "size" : 9100  
        },  
        "comment" : [  
  
        ]  
    }  
]  
  
------  
Removing /0/image   
[  
    {  
        "comment" : [  
  
        ]  
    }  
]  
  
------  
  
```

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-02-15 02:03:20 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2660631471  

An automated preview of the documentation is available at [https://1071.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1071.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2025-02-15 03:07:59 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2660685270  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1071/pullrequest-condensed-c6eaf01.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1071/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1071/pullrequest.html)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2025-02-15 03:35:32 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2660698340  

Why does this PR kill the performance in the benchmarks?  
![image](https://github.com/user-attachments/assets/dcdf20fa-5c69-4ac6-86bb-a713845b1ecb)

---

## Comment 4

> Username: RoyBellingan  
> Created_at: 2025-02-15 13:07:33 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2660919772  

I will try to do another PR with same code, I am confident @vinniefalco  that above was just some temporary fluctuation on the vm running the test.

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2025-02-15 13:58:21 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2660936205  

An automated preview of the documentation is available at [https://1071.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1071.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2025-02-15 15:19:37 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2660968655  

An automated preview of the documentation is available at [https://1071.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1071.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2025-02-15 15:43:38 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2660976690  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1071/pullrequest-condensed-bbf8474.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1071/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1071/pullrequest.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2025-02-15 18:04:58 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2661027976  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1071/pullrequest-condensed-5f2ba45.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1071/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1071/pullrequest.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2025-02-15 22:28:16 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2661130036  

An automated preview of the documentation is available at [https://1071.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1071.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 10

> Username: codecov[bot]  
> Created_at: 2025-02-15 23:06:18 UTC  
> Updated_at: 2025-02-15 23:22:15 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2661140379  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1071?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `0%` with `63 lines` in your changes missing coverage. Please review.  
> Project coverage is 93.06%. Comparing base [(`c02d872`)](https://app.codecov.io/gh/boostorg/json/commit/c02d8721efeb3c4f7b9d86758a48e11edb523ae5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`c829369`)](https://app.codecov.io/gh/boostorg/json/commit/c829369210d768d139fa37bfb015f65d5dc62262?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/1071?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/json/impl/pointer.ipp](https://app.codecov.io/gh/boostorg/json/pull/1071?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fpointer.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcG9pbnRlci5pcHA=) | 0.00% | [63 Missing :warning: ](https://app.codecov.io/gh/boostorg/json/pull/1071?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1071/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1071?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1071      +/-   ##  
===========================================  
- Coverage    93.70%   93.06%   -0.65%       
===========================================  
  Files           91       91                
  Lines         9139     9202      +63       
===========================================  
  Hits          8564     8564                
- Misses         575      638      +63       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/1071?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/value.hpp](https://app.codecov.io/gh/boostorg/json/pull/1071?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fvalue.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `98.83% <ø> (ø)` | |  
| [include/boost/json/impl/pointer.ipp](https://app.codecov.io/gh/boostorg/json/pull/1071?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fpointer.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcG9pbnRlci5pcHA=) | `79.41% <0.00%> (-20.59%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1071?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1071?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c02d872...c829369](https://app.codecov.io/gh/boostorg/json/pull/1071?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2025-02-15 23:32:57 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2661146988  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1071/pullrequest-condensed-7b22a0c.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1071/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1071/pullrequest.html)

---

## Comment 12

> Username: vinniefalco  
> Created_at: 2025-02-17 18:30:01 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2663853484  

@sdarwin once again I question these benchmarks....

---

## Comment 13

> Username: sdarwin  
> Created_at: 2025-02-17 18:57:29 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2663893904  

At least at this moment, there is not a basis to think the benchmark is wrong.  I will run a test and attempt to replicate that result.

---

## Comment 14

> Username: sdarwin  
> Created_at: 2025-02-17 20:33:32 UTC  
> Updated_at: 2025-02-17 20:33:44 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2664025335  

Just as expected, the results can be easily replicated. :-)   See https://github.com/sdarwin/json/pull/20 , which shows the image below.      
If a benchmark is questionable then re-run the same job by pushing a commit with a small whitespace modification and nothing else.  
  
![bench](https://github.com/user-attachments/assets/5f8034a5-e0b2-492a-81a2-bcb0687761b9)

---

## Comment 15

> Username: grisumbras  
> Created_at: 2025-02-19 05:55:09 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2667575554  

I'm not entirely sold on this idea. Given that you also want to return the part of the pointer string that was matched (which is not done for other JSON Pointer functions), it appears to me there's a need for lower-level Pointer functionality, with which users could build their own algorithms.  
  
Alternative idea for a simple "delete at pointer" API: add a special "undefined" state to `value_ref` that erases the corresponding element when used with `set_at_pointer`.

---

## Comment 16

> Username: grisumbras  
> Created_at: 2025-02-19 06:31:06 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2667621511  

@sdarwin the question isn't whether benchmark results are random. You showed us several times that they are not. The question is why they are so much affected by random changes. It could be that the benchmark runner program is not very good, but I remember at least in one case a change in a CMake script (that did not relate to the benchmarks) resulted in benchmark performance degradation.  
  
The only way I can explain this phenomenon is that the build process itself exhausts some resource, or results in some file loading/unloading and that changes the overall performance of the system. Maybe we could try building the benchmarks, storing the build artifacts, and then launching the benchmarks with those artifacts in a fresh container?

---

## Comment 17

> Username: sdarwin  
> Created_at: 2025-02-19 11:43:39 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2668397671  

The current benchmark in this pull request is the following:  
  
![bench8](https://github.com/user-attachments/assets/0c93581e-1103-4cab-90c8-d5f7d3667464)  
  
That is mostly around 3% or less.  Some 0%.   It's not wildly unexpected.  
  
However, @grisumbras you should understand as well as anyone, even a very small code change (a wrongly placed "if", a missing "return", a bit or byte in the wrong place)  can generate a detour in the code path, such that the code is very buggy, and the results are anyone's guess.    It may be accurate and correct for the benchmarks to deviate by 30% or 100%, or 10000%, when there is a bug, a mistake, in the pull request.

---

## Comment 18

> Username: grisumbras  
> Created_at: 2025-02-19 12:19:00 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2668491723  

I wouldn't expect bugs in this PR's code (whether they exist or not) to affect benchmarks, given that none of this new code is ever executed by the benchmarks.  
  
Regardless, my comment should have been considered more generally.  Consider this PR: #1031. It just causes warnings in certain cases. Shouldn't affect runtime at all. And yet several tests are affected quite dramatically.

---

## Comment 19

> Username: RoyBellingan  
> Created_at: 2025-02-19 12:44:13 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2668549014  

https://learn.microsoft.com/en-us/azure/architecture/antipatterns/noisy-neighbor/noisy-neighbor  
  
This is what probably is @grisumbras and @sdarwin .

---

## Comment 20

> Username: sdarwin  
> Created_at: 2025-02-19 12:55:59 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2668576854  

> Consider this PR: https://github.com/boostorg/json/pull/1031.  
  
Checking notes... that exact week was when the newest server khjson1.cpp.al went into production for the first time.    
That's a sea of 0% .  :-)  It's a great success.   My interpretation of that result is:  
- mostly, the code had no affect on benchmarks  
- on specifically "twitter" and "twitterescaped", there was (unknown) benchmark instability and variation.     It wasn't caused by the pull request itself.    However, at the time, we discussed "twitter" and "twitterescaped" briefly, you had made some remarks, and maybe even changed something in the json code, such that those benches improved later on.  
In recent times, an example of margin-of-error in the benchmarks, and no affect from the PR, is in the recent PR 1073.  This is randomness. but pretty good:    
  
![bench15](https://github.com/user-attachments/assets/da5851db-d5d5-4ad3-b479-56c47d8b4302)

---

## Comment 21

> Username: sdarwin  
> Created_at: 2025-02-19 13:02:02 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2668591692  

@RoyBellingan , there is no evidence of "noisy neighbors" usually: this is not a virtual machine on shared hardware, it is a separate server.

---

## Comment 22

> Username: grisumbras  
> Created_at: 2025-02-24 07:02:11 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2677577726  

@RoyBellingan after some thinking, I'm not sure that producing an error when an element is not found makes much sense. The effect of the operation is that the element is not nested in the value. If it already wasn't there, the effect is the same. So, the function should mostly report an error if the pointer string is invalid. In this case, if error is not set and `true` is returned, the element was found and removed, and if error is not set and `false` is returned, the element wasn't found.  
  
An aside, I think the function should be called `erase_at_pointer`, to better match the conventional C++ nomenclature. It's also a tiny bit shorter.  
  
In addition, there's a question of what to do with a pointer that designates the root object (an empty string). As far as I can see your algorithm doesn't handle it in a sensible manner. Even if it did, it obviously cannot actually remove the root value, so I'm inclined to think that this situation should actually be an error. Probably a new one.  
  
I also want you to remove the tracking of the consumed part of the Pointer string. None of the other Pointer functions do that right now, and it would be strange to introduce it only for this function. We have a potential idea on how to implement such tracking in the future.  
  
Finally, for the PR to be eventually merged, it should also contain all the necessary overloads (similar to `set_at_pointer`) and tests. All new lines should be covered by tests, unless there's a reason it can't be reasonably done.

---

## Comment 23

> Username: RoyBellingan  
> Created_at: 2025-02-24 15:22:36 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-2678789214  

I have to say, this   
> he effect of the operation is that the element is not nested in the value  
Is so elegant and correct that... yes! You are totally right  
  
> erase_at_pointer  
I agree on that too, is just me that I mix things sorry.  
  
Thank you very much for the feedback! I will try soon to work on those element!

---

## Comment 24

> Username: cppalliance-bot  
> Created_at: 2025-07-10 00:09:38 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-3054516401  

An automated preview of the documentation is available at [https://1071.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1071.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 25

> Username: cppalliance-bot  
> Created_at: 2025-07-10 01:12:02 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-3054806983  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1071/pullrequest-condensed-318691d.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1071/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1071/pullrequest.html)

---

## Comment 26

> Username: grisumbras  
> Created_at: 2025-08-22 10:11:57 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-3213842504  

Hi! Can you add a few tests that check that the implemented functionality works?

---

## Comment 27

> Username: RoyBellingan  
> Created_at: 2025-08-22 14:44:50 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-3214633759  

oh @grisumbras trust me I would love to have a moment to do that!

---

## Comment 28

> Username: cppalliance-bot  
> Created_at: 2025-12-23 17:48:00 UTC  
> Updated_at: 2026-01-06 04:04:49 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-3687472092  

GCOVR code coverage report [https://1071.json.prtest2.cppalliance.org/gcovr/index.html](https://1071.json.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://1071.json.prtest2.cppalliance.org/genhtml/index.html](https://1071.json.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://1071.json.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://1071.json.prtest2.cppalliance.org/gcovr/coverage_diff.txt)  
  
Build time: 2026-01-06 04:04:47 UTC

---

## Comment 29

> Username: cppalliance-bot  
> Created_at: 2026-01-06 03:52:55 UTC  
> Url: https://github.com/boostorg/json/pull/1071#issuecomment-3713004683  

An automated preview of the documentation is available at [https://1071.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1071.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2026-01-06 03:52:54 UTC

---
