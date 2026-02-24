# #824 write double values with full precision in example pretty.cpp [Closed]

> Username: ofenloch  
> Created at: 2022-12-22 10:16:06 UTC  
> Updated at: 2023-08-29 17:20:28 UTC  
> Closed at: 2023-05-08 07:55:05 UTC  
> Url: https://github.com/boostorg/json/pull/824  

avoid precision issues with double values when parsing a pretty_printed JSON file  
  
see my discussion with @grisumbras in https://github.com/boostorg/json/pull/33#discussion_r1055258619

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-12-22 10:33:37 UTC  
> Updated_at: 2023-01-03 04:02:58 UTC  
> Url: https://github.com/boostorg/json/pull/824#issuecomment-1362673780  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/824?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#824](https://codecov.io/gh/boostorg/json/pull/824?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a6cd8a0) into [develop](https://codecov.io/gh/boostorg/json/commit/8659e7d6949edbb1e88fdff28deeb9b388b5fc28?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8659e7d) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head a6cd8a0 differs from pull request most recent head 5a759b2. Consider uploading reports for the commit 5a759b2 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/824/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/824?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #824   +/-   ##  
========================================  
  Coverage    99.01%   99.01%             
========================================  
  Files           70       70             
  Lines         6883     6883             
========================================  
  Hits          6815     6815             
  Misses          68       68             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/824?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/824?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [8659e7d...5a759b2](https://codecov.io/gh/boostorg/json/pull/824?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-22 10:34:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/824#pullrequestreview-1227462501  

📁 example/pretty.cpp

```diff
 116 |+         // see https://stackoverflow.com/questions/554063/how-do-i-print-a-double-value-with-full-precision-using-cout)
 117 |+         os.precision(std::numeric_limits<double>().digits10);
 118 |         os << jv.get_double();
```

> Username: vinniefalco  
> Created_at: 2022-12-22 10:34:34 UTC  
> Url: https://github.com/boostorg/json/pull/824#discussion_r1055322230  

this should be using the library function to output the number

> Username: ofenloch  
> Created_at: 2022-12-22 11:01:10 UTC  
> Url: https://github.com/boostorg/json/pull/824#discussion_r1055343927  

Do you mean this?  
  
```cpp  
os << jsonvalue;  
```

> Username: grisumbras  
> Created_at: 2022-12-22 11:04:29 UTC  
> Updated_at: 2022-12-22 11:04:43 UTC  
> Url: https://github.com/boostorg/json/pull/824#discussion_r1055346447  

That should work

> Username: ofenloch  
> Created_at: 2022-12-22 11:12:21 UTC  
> Url: https://github.com/boostorg/json/pull/824#discussion_r1055352641  

OK, I updated my code. Tests run fine.  
  
https://github.com/ofenloch/json/commit/a7aaa839a841498827af4e7b116d02615db9c701  
  
Should I create a new pull request?

> Username: grisumbras  
> Created_at: 2023-01-02 14:33:09 UTC  
> Url: https://github.com/boostorg/json/pull/824#discussion_r1060067085  

No


---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-12-22 12:24:35 UTC  
> Url: https://github.com/boostorg/json/pull/824#issuecomment-1362778592  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/824/pullrequest-condensed-6317e0e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/824/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/824/pullrequest.html)

---

## Comment 4

> Username: grisumbras  
> Created_at: 2023-01-02 14:33:34 UTC  
> Url: https://github.com/boostorg/json/pull/824#issuecomment-1368996803  

Please, squash the commits and rebase on current develop

---

## Comment 5

> Username: grisumbras  
> Created_at: 2023-01-02 15:21:31 UTC  
> Url: https://github.com/boostorg/json/pull/824#issuecomment-1369032021  

I can also do it myself. The commit will retain your authorship, but I will have to merge via a different PR, and this one will be closed and not marked as merged.

---

## Comment 6

> Username: ofenloch  
> Created_at: 2023-01-02 16:18:03 UTC  
> Url: https://github.com/boostorg/json/pull/824#issuecomment-1369070566  

Sorry, but I don't know how to squash a commit.  
  
I don't use GitHub desktop, and I just can't figure it out how to do it in the web interface.

---

## Comment 7

> Username: grisumbras  
> Created_at: 2023-01-02 17:35:21 UTC  
> Updated_at: 2023-01-02 17:37:08 UTC  
> Url: https://github.com/boostorg/json/pull/824#issuecomment-1369112340  

You don't do it in web interface, you do it locally, then force push .  
```  
git rebase -i develop  
```  
It opens a text editor with the list of commits since develop with actions that you will do. Replace "pick" near the second one with "squash".

---

## Comment 8

> Username: ofenloch  
> Created_at: 2023-01-03 03:51:20 UTC  
> Url: https://github.com/boostorg/json/pull/824#issuecomment-1369367752  

Thanks for the info. I did what you told me to do.  
  
Currently I'm working my way through "Getting Started with Modular Boost Library Maintenance" to do it properly the next time ...

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-01-03 04:59:42 UTC  
> Url: https://github.com/boostorg/json/pull/824#issuecomment-1369395215  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/824/pullrequest-condensed-7efb3c4.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/824/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/824/pullrequest.html)

---

## Comment 10

> Username: grisumbras  
> Created_at: 2023-05-08 07:55:05 UTC  
> Url: https://github.com/boostorg/json/pull/824#issuecomment-1537915921  

Merged in 740dbaa7a66a0b54de7a6e08c86f160468f8a33d

---

## Comment 11

> Username: pdimov  
> Created_at: 2023-05-08 10:14:58 UTC  
> Url: https://github.com/boostorg/json/pull/824#issuecomment-1538123838  

The string, bool and null cases can also use `os << jv`.

---
