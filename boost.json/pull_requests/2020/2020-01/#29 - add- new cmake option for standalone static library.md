# #29 add: new cmake option for standalone static library [Closed]

> Username: ayounes-synaptics  
> Created at: 2020-01-08 08:25:37 UTC  
> Updated at: 2020-02-14 01:46:42 UTC  
> Closed at: 2020-02-14 01:46:42 UTC  
> Url: https://github.com/boostorg/json/pull/29  

BOOST_JSON_STANDALONE: new cmake option to enable compilation of  
boost::json as a static standalone dependency without any  
dependency, it also also compiles the examples.  
  
warning: C++-17 is mandatory in this mode

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2020-01-08 12:58:04 UTC  
> Url: https://github.com/boostorg/json/pull/29#issuecomment-572036358  

Wonderful. This is the first time I'm actually happy about seeing a cmake `option` !!

---

## Comment 2

> Username: ayounes-synaptics  
> Created_at: 2020-01-08 16:01:21 UTC  
> Url: https://github.com/boostorg/json/pull/29#issuecomment-572136054  

The failed test seems to be an environment issue:  
  
gpg: requesting key BA9EF27F from hkp server keyserver.ubuntu.com  
Error: retrieving gpg key timed out.  
gpg: keyserver timed out  
gpg: keyserver receive failed: keyserver error  
The command "sudo -E apt-add-repository -y "ppa:ubuntu-toolchain-r/test"" failed and exited with 1 during .

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2020-01-08 20:52:41 UTC  
> Url: https://github.com/boostorg/json/pull/29#issuecomment-572251768  

> The failed test seems to be an environment issue:   
  
Yeah, usually when I restart the build errors like that go away. I'll do it now.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2020-01-08 20:56:26 UTC  
> Updated_at: 2020-01-08 20:56:30 UTC  
> Url: https://github.com/boostorg/json/pull/29#issuecomment-572253112  

# [Codecov](https://codecov.io/gh/vinniefalco/json/pull/29?src=pr&el=h1) Report  
> Merging [#29](https://codecov.io/gh/vinniefalco/json/pull/29?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/json/commit/dda9d88bac7d7bb0698e237260d4579bf429bbda?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/json/pull/29/graphs/tree.svg?width=650&token=HNiMmIjyKi&height=150&src=pr)](https://codecov.io/gh/vinniefalco/json/pull/29?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #29   +/-   ##  
========================================  
  Coverage    99.27%   99.27%             
========================================  
  Files           57       57             
  Lines         4815     4815             
========================================  
  Hits          4780     4780             
  Misses          35       35  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/json/pull/29?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/json/pull/29?src=pr&el=footer). Last update [dda9d88...1fb1286](https://codecov.io/gh/vinniefalco/json/pull/29?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2020-01-08 22:57:21 UTC  
> Url: https://github.com/boostorg/json/pull/29#issuecomment-572298262  

voila

---

## Comment 6

> Username: ayounes-synaptics  
> Created_at: 2020-01-09 07:27:07 UTC  
> Url: https://github.com/boostorg/json/pull/29#issuecomment-572428627  

> voila  
  
Merci :-)

---
