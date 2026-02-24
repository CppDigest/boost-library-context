# #1744 Run upstream zlib against our tests to ensure compatibility [Closed]

> Username: AeroStun  
> Created at: 2019-10-23 12:50:56 UTC  
> Updated at: 2019-11-06 12:54:05 UTC  
> Closed at: 2019-11-06 12:54:05 UTC  
> Url: https://github.com/boostorg/beast/pull/1744  

From #1719   
Comments and reviews are very welcome  
  
[A Travis-CI build simulating this pull request](https://travis-ci.org/AeroStun/beast/builds/601784832) has already been run and did yield a positive outcome

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2019-10-23 12:53:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1744#pullrequestreview-305864948  

📁 test/beast/zlib/deflate_stream.cpp

```diff
 434 |         doMatrix(c, "Hello, world!", &self::doDeflate1_beast);
 435 |         doMatrix(c, "Hello, world!", &self::doDeflate2_beast);
 436 |+         log << "no-silence keepalive" << std::endl;
```

> Username: vinniefalco  
> Created_at: 2019-10-23 12:53:49 UTC  
> Updated_at: 2019-11-02 15:14:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1744#discussion_r338028305  

oh no... why?

> Username: AeroStun  
> Created_at: 2019-10-23 12:56:09 UTC  
> Updated_at: 2019-11-02 15:14:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1744#discussion_r338029458  

The Travis build using valgrind times out after 10 minutes of silence  
Since we now test both Beast.zlib and upstream zlib, the build exceeds the silence limit

> Username: AeroStun  
> Created_at: 2019-10-23 13:05:11 UTC  
> Updated_at: 2019-11-02 15:14:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1744#discussion_r338034148  

See [this Travis job](https://travis-ci.org/AeroStun/beast/jobs/601715039)


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2019-10-23 12:55:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1744#pullrequestreview-305866009  

📁 test/beast/zlib/deflate_stream.cpp

```diff
  26 | class deflate_stream_test : public beast::unit_test::suite
  27 | {
  28 |+     struct ICompressor {
```

> Username: vinniefalco  
> Created_at: 2019-10-23 12:55:22 UTC  
> Updated_at: 2019-11-02 15:14:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1744#discussion_r338029085  

These are uuuuuugly but I guess that's a consequence of the zlib api...

> Username: AeroStun  
> Created_at: 2019-10-23 12:58:26 UTC  
> Updated_at: 2019-11-02 15:14:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1744#discussion_r338030684  

There's even a `const_cast` in there, because zlib wants its inputs to be non-const, although it doesn't modify them, while Beast rightfully requests a non-const version.  
If any improvements come to your mind to make this slightly better, I'll happily apply them

> Username: vinniefalco  
> Created_at: 2019-10-23 13:06:11 UTC  
> Updated_at: 2019-11-02 15:14:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1744#discussion_r338034634  

I want to believe that there is a more modern abstraction still waiting to be discovered which streamlines the inflate/deflate API and makes it easier to document and understand. Eventually I will investigate and hopefully find it. In the long term, the zlib part of Beast needs to be moved into its own repository and Boost library. In order to pass the Boost review though, some cleaning up will be needed (this doesn't have to happen now). Having it in a separate repository will make things easier on Travis for Beast and Zlib.


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2019-10-23 18:34:07 UTC  
> Updated_at: 2019-11-02 18:16:03 UTC  
> Url: https://github.com/boostorg/beast/pull/1744#issuecomment-545578641  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1744?src=pr&el=h1) Report  
> Merging [#1744](https://codecov.io/gh/boostorg/beast/pull/1744?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/bb85859e96d2815c0a49701733ce4d15f7b156d0?src=pr&el=desc) will **increase** coverage by `0.03%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1744/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1744?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1744      +/-   ##  
===========================================  
+ Coverage    95.13%   95.17%   +0.03%       
===========================================  
  Files          156      156                
  Lines        11943    11947       +4       
===========================================  
+ Hits         11362    11370       +8       
+ Misses         581      577       -4  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1744?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1744/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.6% <100%> (+0.41%)` | :arrow_up: |  
| [include/boost/beast/zlib/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/1744/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2ltcGwvZXJyb3IuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/zlib/deflate\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1744/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RlZmxhdGVfc3RyZWFtLmhwcA==) | `100% <0%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1744?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1744?src=pr&el=footer). Last update [bb85859...cf9de17](https://codecov.io/gh/boostorg/beast/pull/1744?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: AeroStun  
> Created_at: 2019-10-24 12:12:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1744#issuecomment-545888693  

Are there any changes/additions you'd like to see added to that PR, or do you consider it staged for the next version ?

---

## Comment 5

> Username: AeroStun  
> Created_at: 2019-10-31 22:26:34 UTC  
> Url: https://github.com/boostorg/beast/pull/1744#issuecomment-548593756  

Rebased on version 275

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2019-11-02 14:36:47 UTC  
> Url: https://github.com/boostorg/beast/pull/1744#issuecomment-549049299  

This introduces no changes to the public interfaces correct?

---

## Comment 7

> Username: AeroStun  
> Created_at: 2019-11-02 14:40:33 UTC  
> Url: https://github.com/boostorg/beast/pull/1744#issuecomment-549049630  

It does, in the form of a typo correction in an enum name  
https://github.com/boostorg/beast/pull/1744/commits/6b5eb041f1cc5559cb008fad766d904a33eae469

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2019-11-02 14:42:35 UTC  
> Url: https://github.com/boostorg/beast/pull/1744#issuecomment-549049766  

In **theory** we should use  
```  
    invalid_code_lengths,  
#ifndef BOOST_BEAST_NO_DEPRECATED  
    invalid_code_lenths = invalid_code_lengths,  
#endif  
```  
  
in practice...it doesn't matter, because no one is using these APIs. In fact 99% of users probably don't know they exist...

---

## Comment 9

> Username: AeroStun  
> Created_at: 2019-11-02 15:02:24 UTC  
> Url: https://github.com/boostorg/beast/pull/1744#issuecomment-549051470  

Okay  
Still thanks for showing me the right way of doing it; will definitely save me time if I encounter a similar issue again.

---

## Comment 10

> Username: AeroStun  
> Created_at: 2019-11-02 15:15:02 UTC  
> Url: https://github.com/boostorg/beast/pull/1744#issuecomment-549052743  

Just added your snippet, to do the right thing  
No reason for Travis to break.

---
