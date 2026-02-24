# #1827 CircleCI sandbox [Closed]

> Username: madmongo1  
> Created at: 2020-01-27 18:50:40 UTC  
> Updated at: 2020-02-24 15:45:34 UTC  
> Closed at: 2020-02-24 15:45:34 UTC  
> Url: https://github.com/boostorg/beast/pull/1827  

First stab at getting circleci integration working  
runs tests and compiles examples using latest gcc with latest c++ version  
  
this is not a request to pull, just an informational

---

## Review 1 [Commented]

> Username: djarek  
> Created_at: 2020-01-27 18:54:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1827#pullrequestreview-348895914  

📁 .circleci/config.yml

```diff
  44 |+             name: bootstrap
  45 |+             working_directory: /tmp/boost
  46 |+             command: ./bootstrap.sh
```

> Username: djarek  
> Created_at: 2020-01-27 18:54:29 UTC  
> Updated_at: 2020-02-04 16:15:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1827#discussion_r371419609  

You can use this script to grab just the necessary dependencies: https://github.com/boostorg/beast/blob/develop/tools/get-boost.sh

> Username: madmongo1  
> Created_at: 2020-01-28 09:57:20 UTC  
> Updated_at: 2020-02-04 16:15:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1827#discussion_r371703905  

I'm wary of that script because each submodule is explicitly named. Submodules seem to come and go in master boost's develop branch.

> Username: vinniefalco  
> Created_at: 2020-01-28 14:14:06 UTC  
> Updated_at: 2020-02-04 16:15:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1827#discussion_r371825572  

No that's not correct, changes to submodules are infrequent.

> Username: madmongo1  
> Created_at: 2020-01-29 21:26:49 UTC  
> Updated_at: 2020-02-04 16:15:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1827#discussion_r372638791  

I was caught out by a change to submodules in boost develop branch just last week!

> Username: vinniefalco  
> Created_at: 2020-01-30 02:16:36 UTC  
> Updated_at: 2020-02-04 16:15:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1827#discussion_r372730153  

In other words, `int sample_size()` returned 1

> Username: madmongo1  
> Created_at: 2020-01-30 14:00:38 UTC  
> Updated_at: 2020-02-04 16:15:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1827#discussion_r372963275  

Thank you for bringing the script to my attention.   
  
I have used pertinent details from it to improve the throughput of my circleci configuration file.  
  
I have chosen not to use the script as-is for two reasons:  
  
1. It would construct a git tree in the circleci host that does not exactly match the one in my workflow. This would potentially make debugging of issues more difficult for me through circle's ssh debugging interface.  
  
2. The double-entry bookeeping required to keep the script in sync with master and develop of the git root repo is unecessary work. The discovered git repo already has this information intrinsically.  
  
If there are any technical issues that prevent the correct functioning of a circleci build of beast in this PR please kindly bring them to my attention.  
  
If any part of the submitted configuration file can be proven subobtimal I would be happy to hear about it.  
  
As things stand I believe that this PR is perfectly correct.


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-01-27 19:10:05 UTC  
> Updated_at: 2020-02-04 16:30:42 UTC  
> Url: https://github.com/boostorg/beast/pull/1827#issuecomment-578904073  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1827?src=pr&el=h1) Report  
> Merging [#1827](https://codecov.io/gh/boostorg/beast/pull/1827?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/c9d5049307925bccaf2f5e57c127db1a1c78e178?src=pr&el=desc) will **decrease** coverage by `0.02%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1827/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1827?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1827      +/-   ##  
===========================================  
- Coverage    95.19%   95.16%   -0.03%       
===========================================  
  Files          156      156                
  Lines        11956    11956                
===========================================  
- Hits         11381    11378       -3       
- Misses         575      578       +3  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1827?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/1827/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `96.42% <0%> (-0.9%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1827/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.37% <0%> (-0.2%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1827/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.5% <0%> (-0.11%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1827?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1827?src=pr&el=footer). Last update [c9d5049...6b6e647](https://codecov.io/gh/boostorg/beast/pull/1827?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: madmongo1  
> Created_at: 2020-01-28 10:01:30 UTC  
> Url: https://github.com/boostorg/beast/pull/1827#issuecomment-579168254  

OK, this PR has just become a pull request.  
  
Circleci build and test is working. I'd like to use it as a base for further development but there is value for me in it going into the develop branch as until it does, I'll get spurious circleci errors whenever I push a branch that is based on develop.

---

## Comment 4

> Username: madmongo1  
> Created_at: 2020-01-28 13:04:58 UTC  
> Url: https://github.com/boostorg/beast/pull/1827#issuecomment-579235852  

What?? Why is appveyor reporting a failure when nothing in it failed?

---

## Review 5 [Commented]

> Username: djarek  
> Created_at: 2020-01-30 17:16:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1827#pullrequestreview-350998662  

📁 .circleci/config.yml

```diff
  31 |+             working_directory: /tmp/boost
  32 |+             command: |
  33 |+               git submodule update --init --recursive --depth 1 --jobs 4
```

> Username: djarek  
> Created_at: 2020-01-30 17:16:57 UTC  
> Updated_at: 2020-02-04 16:15:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1827#discussion_r373082399  

A depth=1 submodule update may result in spurious failures whenever somebody commits to a dependency and the root repo isn't updated yet.

> Username: madmongo1  
> Created_at: 2020-01-30 20:55:26 UTC  
> Updated_at: 2020-02-04 16:15:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1827#discussion_r373188554  

confirmed. I'll use 10 as per the script


---
