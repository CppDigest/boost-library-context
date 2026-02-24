# #804 Fixes for appveyor and travis [Open]

> Username: Zialus  
> Created at: 2018-12-24 19:18:18 UTC  
> Updated at: 2021-01-15 07:04:31 UTC  
> Url: https://github.com/boostorg/compute/pull/804  

This should make all the current configuration on appveyor and travis build and pass the tests.  
  
I've added two extra configs. VS2017 on Windows and GCC on OSX.  
  
There's a warning while using GCC on OSX that is triggering -Werror and causing that particular setup to fail. I'm not sure if that is expected behaviour.

---

## Comment 1

> Username: jszuppe  
> Created_at: 2018-12-24 22:00:33 UTC  
> Updated_at: 2018-12-24 22:16:18 UTC  
> Url: https://github.com/boostorg/compute/pull/804#issuecomment-449773676  

I'll take a look at it. I'm actually also working on fixing/changing Travis CI script (see https://github.com/jszuppe/compute/tree/fix_travis_ci), but I'll definitely include what you did with AppVeyor if it works ok. I'll be doing this during the week, I should finish in the weekend.

---

## Comment 2

> Username: jszuppe  
> Created_at: 2018-12-24 22:01:19 UTC  
> Url: https://github.com/boostorg/compute/pull/804#issuecomment-449773718  

I was thinking about removing AMD APP SDK, which is deprecated, and using Intel OpenCL instead.

---

## Comment 3

> Username: Zialus  
> Created_at: 2018-12-25 14:02:57 UTC  
> Updated_at: 2018-12-25 14:03:33 UTC  
> Url: https://github.com/boostorg/compute/pull/804#issuecomment-449852767  

Please clean caches and re-run CI. The remaining failures are warnings being treated as errors, I think.

---

## Comment 4

> Username: Zialus  
> Created_at: 2018-12-25 22:45:31 UTC  
> Updated_at: 2018-12-25 22:47:28 UTC  
> Url: https://github.com/boostorg/compute/pull/804#issuecomment-449877584  

Ok, ignoring the warnings still doesn't make all tests pass. But everything else seems to be working.

---

## Comment 5

> Username: Zialus  
> Created_at: 2018-12-26 19:57:14 UTC  
> Url: https://github.com/boostorg/compute/pull/804#issuecomment-450017035  

Do you want me to do a rebase and include only the appveyor changes?

---

## Comment 6

> Username: jszuppe  
> Created_at: 2018-12-27 10:56:55 UTC  
> Url: https://github.com/boostorg/compute/pull/804#issuecomment-450129033  

> Do you want me to do a rebase and include only the appveyor changes?  
  
You can do separate PR for that. As for `.travis.yml` changes I planned to cherry-pick commits which added Intel Platform.

---

## Comment 7

> Username: Zialus  
> Created_at: 2018-12-27 15:12:16 UTC  
> Url: https://github.com/boostorg/compute/pull/804#issuecomment-450168546  

Here's [something](https://ci.appveyor.com/project/Zialus/compute/builds/21241647) you might be interested in checking out.  Is that behavior on the Ubuntu images expected?  
  
I'll open a PR with a windows only appveyor config.

---

## Comment 8

> Username: jszuppe  
> Created_at: 2018-12-27 15:28:15 UTC  
> Url: https://github.com/boostorg/compute/pull/804#issuecomment-450172029  

If you mean the compilation error on 18.04 (in general when using g++ >=6), then I'll be looking into this after adding Intel Platform 2.0 and 2.1 tests to Travis CI script. If you mean the problem on 16.04, then it's the first time I see it.

---

## Comment 9

> Username: jszuppe  
> Created_at: 2018-12-27 15:36:11 UTC  
> Url: https://github.com/boostorg/compute/pull/804#issuecomment-450173708  

btw. Have you looked into using Windows on Travis CI?

---

## Comment 10

> Username: Zialus  
> Created_at: 2018-12-27 17:55:57 UTC  
> Url: https://github.com/boostorg/compute/pull/804#issuecomment-450200595  

I have checked it out in the past, but it was still pretty green.  
  
Here's something I just cobbled up together https://travis-ci.com/Zialus/compute/builds/95828633

---

## Comment 11

> Username: coveralls  
> Created_at: 2018-12-30 03:46:41 UTC  
> Updated_at: 2021-01-15 07:04:31 UTC  
> Url: https://github.com/boostorg/compute/pull/804#issuecomment-450537251  

[![Coverage Status](https://coveralls.io/builds/36347393/badge)](https://coveralls.io/builds/36347393)  
  
Coverage decreased (-14.8%) to 69.244% when pulling **8f6f1df8758c2477a30c0204c634c602ecc8aa78 on Zialus:ci-fixes** into **2135633bc7f6bddaf09ae409a63a83174e89c881 on boostorg:develop**.

---

## Comment 12

> Username: jszuppe  
> Created_at: 2018-12-30 12:05:28 UTC  
> Url: https://github.com/boostorg/compute/pull/804#issuecomment-450556316  

I think you can close this PR and open two new PR for your changes regarding OSX and Windows that would be based on current `develop` branch. Of course if you have time finalising them.

---

## Comment 13

> Username: Zialus  
> Created_at: 2018-12-31 04:36:32 UTC  
> Url: https://github.com/boostorg/compute/pull/804#issuecomment-450607469  

I might try that tomorrow. Is there a reason appveyor isn't getting triggered with the new commits?

---

## Comment 14

> Username: jszuppe  
> Created_at: 2018-12-31 08:08:58 UTC  
> Url: https://github.com/boostorg/compute/pull/804#issuecomment-450618426  

> Is there a reason appveyor isn't getting triggered with the new commits?  
  
Webhook stopped working and I don't have access to add it.

---

## Comment 15

> Username: Zialus  
> Created_at: 2019-01-01 20:21:52 UTC  
> Updated_at: 2019-01-01 20:22:13 UTC  
> Url: https://github.com/boostorg/compute/pull/804#issuecomment-450754493  

Just to be clear, for this new PR should I branch of from `develop` instead of `master`(unlike what's stated in contribute.md)?

---

## Comment 16

> Username: jszuppe  
> Created_at: 2019-01-01 20:29:25 UTC  
> Url: https://github.com/boostorg/compute/pull/804#issuecomment-450754907  

Yes. When there are already changes in develop that would cause a conflicts it's better to base changes on develop.

---
