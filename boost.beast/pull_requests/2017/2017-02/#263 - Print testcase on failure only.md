# #263 Print testcase on failure only [Closed]

> Username: bachase  
> Created at: 2017-02-10 16:34:47 UTC  
> Updated at: 2017-07-25 17:44:18 UTC  
> Closed at: 2017-02-10 18:02:07 UTC  
> Url: https://github.com/boostorg/beast/pull/263  

Addresses #166 by only reporting the testcase name if the testcase has a failure.  
  
I'm open to making this configurable instead if that is important.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-02-10 16:36:11 UTC  
> Url: https://github.com/boostorg/beast/pull/263#issuecomment-278993083  

IDK how I feel about this. Can you provide an example of before and after output for a suitable exemplar?

---

## Comment 2

> Username: bachase  
> Created_at: 2017-02-10 16:58:57 UTC  
> Url: https://github.com/boostorg/beast/pull/263#issuecomment-278999470  

I find this useful for cases when I've rebased or just want to see if I've broken anything. In those cases, the testcase names for successful cases are just noise.    
  
Below are from ripple with a contrived error in two cases:  
[no.spam.txt](https://github.com/vinniefalco/Beast/files/767285/no.spam.txt)  
[spam.txt](https://github.com/vinniefalco/Beast/files/767286/spam.txt)  
  
Generally, I'm not sure when always printing the testcase name is useful.  When filtering the tests being run?

---

## Comment 3

> Username: codecov-io  
> Created_at: 2017-02-10 17:02:45 UTC  
> Updated_at: 2017-02-10 17:02:58 UTC  
> Url: https://github.com/boostorg/beast/pull/263#issuecomment-279000605  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/263?src=pr&el=h1) Report  
> Merging [#263](https://codecov.io/gh/vinniefalco/Beast/pull/263?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/9f10b11eff58aeb793b673c8a8cb6e2bee3db621?src=pr&el=desc) will **not change** coverage.  
  
  
```diff  
@@           Coverage Diff           @@  
##           master     #263   +/-   ##  
=======================================  
  Coverage   95.01%   95.01%             
=======================================  
  Files          88       88             
  Lines        6241     6241             
=======================================  
  Hits         5930     5930             
  Misses        311      311  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/263?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/263?src=pr&el=footer). Last update [9f10b11...8483a5f](https://codecov.io/gh/vinniefalco/Beast/compare/9f10b11eff58aeb793b673c8a8cb6e2bee3db621...8483a5fbfef17df1e8e84f01549bf03e1e67b011?el=footer&src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2017-02-10 17:09:14 UTC  
> Updated_at: 2017-02-10 17:09:26 UTC  
> Url: https://github.com/boostorg/beast/pull/263#issuecomment-279002450  

Can we at least print the name of the unit test even if there's no failures?  
  
For example, given:  
```  
ripple.tx.Taker XRP Quantization: input  
ripple.tx.Taker XRP Quantization: output  
ripple.tx.Taker IOU to IOU  
ripple.consensus.Consensus single peer  
ripple.consensus.Consensus 5 connected peers  
ripple.consensus.Consensus slow peer  
ripple.consensus.Consensus close time disagree  
#1 failed: Consensus_test.cpp(190)  
#2 failed: Consensus_test.cpp(190)  
#3 failed: Consensus_test.cpp(190)  
#4 failed: Consensus_test.cpp(190)  
#5 failed: Consensus_test.cpp(190)  
#6 failed: Consensus_test.cpp(190)  
ripple.consensus.Consensus test clique fork  
#6 failed: Consensus_test.cpp(232)  
#7 failed: Consensus_test.cpp(232)  
#8 failed: Consensus_test.cpp(232)  
#9 failed: Consensus_test.cpp(232)  
#10 failed: Consensus_test.cpp(232)  
#11 failed: Consensus_test.cpp(232)  
```  
  
We could instead print:  
```  
ripple.tx.Taker  
ripple.consensus.Consensus close time disagree  
#1 failed: Consensus_test.cpp(190)  
#2 failed: Consensus_test.cpp(190)  
#3 failed: Consensus_test.cpp(190)  
#4 failed: Consensus_test.cpp(190)  
#5 failed: Consensus_test.cpp(190)  
#6 failed: Consensus_test.cpp(190)  
ripple.consensus.Consensus test clique fork  
#6 failed: Consensus_test.cpp(232)  
#7 failed: Consensus_test.cpp(232)  
#8 failed: Consensus_test.cpp(232)  
#9 failed: Consensus_test.cpp(232)  
#10 failed: Consensus_test.cpp(232)  
#11 failed: Consensus_test.cpp(232)  
```  
  
?

---

## Comment 5

> Username: bachase  
> Created_at: 2017-02-10 17:10:28 UTC  
> Url: https://github.com/boostorg/beast/pull/263#issuecomment-279002786  

Sure, that seems like a fair compromise.  I'll force push that.

---

## Comment 6

> Username: mellery451  
> Created_at: 2017-02-10 17:11:48 UTC  
> Url: https://github.com/boostorg/beast/pull/263#issuecomment-279003143  

Should we also print the testcase name if/when `log` is written to during the test? Here is an example from a current test:  
```  
ripple.ripple_data.BuildInfo version packing  
    Ripple Version: 0.60.0-b4  
    Protocol Version: 1.2  
```  
...otherwise we get log messages without any context. I think @vinniefalco 's suggestion about keeping the test suite name alleviates this concern.

---

## Comment 7

> Username: bachase  
> Created_at: 2017-02-10 17:14:58 UTC  
> Url: https://github.com/boostorg/beast/pull/263#issuecomment-279003973  

@mellery451 I can do either. I've not had a reason to use log before, so I'll defer to others on what makes more sense.

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2017-02-10 17:16:48 UTC  
> Url: https://github.com/boostorg/beast/pull/263#issuecomment-279004522  

If a unit test produces any output using `log` its important for the `reporter` to show the test case or unit test name, or else not only will there be no context but it will look like the output is coming from the _previous_ test that ran.

---

## Comment 9

> Username: bachase  
> Created_at: 2017-02-10 18:02:07 UTC  
> Url: https://github.com/boostorg/beast/pull/263#issuecomment-279018774  

This makes more sense as a configurable option, since listing all the testcases does provide value (to show what you are testing).    
  
I'm going to close for now and will eventually get around to an improved version.

---
