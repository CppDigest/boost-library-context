# #12 Travis CI updates: [Closed]

> Username: sublimator  
> Created at: 2016-05-15 11:45:47 UTC  
> Updated at: 2016-05-24 10:04:32 UTC  
> Closed at: 2016-05-24 06:18:01 UTC  
> Url: https://github.com/boostorg/beast/pull/12  

- Use clang 3.8  
- Add msan/usan variants  
- Make sure (correct) llvm-symbolizer is on PATH  
- Run autobahn/valgrind tests when target branch in {master, develop}  
- Add coveralls

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-05-15 12:02:38 UTC  
> Url: https://github.com/boostorg/beast/pull/12#issuecomment-219281922  

[![Coverage Status](https://coveralls.io/builds/6186696/badge)](https://coveralls.io/builds/6186696)  
  
Changes Unknown when pulling **f33b4d6c68ec814101c718a1ff7b35cc7507947d on sublimator:travis-updates** into *\* on vinniefalco:master**.

---

## Comment 2

> Username: codecov-io  
> Created_at: 2016-05-15 12:02:38 UTC  
> Updated_at: 2016-05-18 08:30:52 UTC  
> Url: https://github.com/boostorg/beast/pull/12#issuecomment-219281923  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/12?src=pr) is **92.44%**  
  
> Merging [#12](https://codecov.io/gh/vinniefalco/Beast/pull/12?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will not change coverage  
  
``` diff  
@@             master        #12   diff @@  
==========================================  
  Files            69         69            
  Lines          4630       4630            
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
  Hits           4280       4280            
  Misses          350        350            
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last updated by [c0952e5...f33b4d6](https://codecov.io/gh/vinniefalco/Beast/compare/c0952e54db7bd519440dc0611db7347cb048296d...f33b4d6c68ec814101c718a1ff7b35cc7507947d)

---

## Comment 3

> Username: coveralls  
> Created_at: 2016-05-18 09:05:01 UTC  
> Url: https://github.com/boostorg/beast/pull/12#issuecomment-219968048  

[![Coverage Status](https://coveralls.io/builds/6226966/badge)](https://coveralls.io/builds/6226966)  
  
Changes Unknown when pulling **3734b7abd868e6b82d9f557697a3f948387432d8 on sublimator:travis-updates** into *\* on vinniefalco:master**.

---

## Comment 4

> Username: coveralls  
> Created_at: 2016-05-18 09:05:21 UTC  
> Url: https://github.com/boostorg/beast/pull/12#issuecomment-219968158  

[![Coverage Status](https://coveralls.io/builds/6226825/badge)](https://coveralls.io/builds/6226825)  
  
Changes Unknown when pulling **3734b7abd868e6b82d9f557697a3f948387432d8 on sublimator:travis-updates** into *\* on vinniefalco:master**.

---

## Comment 5

> Username: coveralls  
> Created_at: 2016-05-18 11:05:02 UTC  
> Url: https://github.com/boostorg/beast/pull/12#issuecomment-219994353  

[![Coverage Status](https://coveralls.io/builds/6228437/badge)](https://coveralls.io/builds/6228437)  
  
Changes Unknown when pulling **62f764704432776cf5bdea0879f9b2c4fa0f45b4 on sublimator:travis-updates** into *\* on vinniefalco:master**.

---

## Comment 6

> Username: coveralls  
> Created_at: 2016-05-18 12:05:36 UTC  
> Url: https://github.com/boostorg/beast/pull/12#issuecomment-220006337  

[![Coverage Status](https://coveralls.io/builds/6229064/badge)](https://coveralls.io/builds/6229064)  
  
Changes Unknown when pulling **fb961869404ef106f1a7f672096a2be3679ea896 on sublimator:travis-updates** into *\* on vinniefalco:master**.

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2016-05-18 19:47:39 UTC  
> Updated_at: 2016-05-18 19:47:47 UTC  
> Url: https://github.com/boostorg/beast/pull/12#issuecomment-220137422  

Does the **`ws`** branch need this commit? Or a variant of it?

---

## Comment 8

> Username: sublimator  
> Created_at: 2016-05-19 07:08:31 UTC  
> Updated_at: 2016-05-19 07:08:52 UTC  
> Url: https://github.com/boostorg/beast/pull/12#issuecomment-220244613  

Yes, cherry-pick this commit (after squashing) for #14, replacing the existing one there

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2016-05-22 13:47:01 UTC  
> Url: https://github.com/boostorg/beast/pull/12#issuecomment-220833362  

Please squash and rebase, there are merge conflicts.

---

## Comment 10

> Username: sublimator  
> Created_at: 2016-05-22 13:56:26 UTC  
> Url: https://github.com/boostorg/beast/pull/12#issuecomment-220833819  

Ok, will rebase on top of the ws branch

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2016-05-24 09:55:05 UTC  
> Url: https://github.com/boostorg/beast/pull/12#issuecomment-221221535  

??

---

## Comment 12

> Username: sublimator  
> Created_at: 2016-05-24 09:56:21 UTC  
> Url: https://github.com/boostorg/beast/pull/12#issuecomment-221221850  

What's the ?? regarding?  
On 24 May 2016 16:55, "Vinnie Falco" notifications@github.com wrote:  
  
> ??  
>   
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly or view it on GitHub  
> https://github.com/vinniefalco/Beast/pull/12#issuecomment-221221535

---

## Comment 13

> Username: vinniefalco  
> Created_at: 2016-05-24 09:57:06 UTC  
> Url: https://github.com/boostorg/beast/pull/12#issuecomment-221222038  

Why is this closed?

---

## Comment 14

> Username: sublimator  
> Created_at: 2016-05-24 10:04:31 UTC  
> Url: https://github.com/boostorg/beast/pull/12#issuecomment-221223767  

It was targeting master. I opened another WIP branch based on ws with a  
commit to cherry pick onto ur ws branch in place of the current one.

---
