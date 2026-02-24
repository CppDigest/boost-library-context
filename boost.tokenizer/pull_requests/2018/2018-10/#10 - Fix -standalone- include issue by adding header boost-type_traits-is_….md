# #10 Fix "standalone" include issue by adding header boost/type_traits/is_… [Merged]

> Username: matovitch  
> Created at: 2018-10-22 15:38:34 UTC  
> Updated at: 2018-10-22 22:58:10 UTC  
> Merged at: 2018-10-22 17:18:50 UTC  
> Closed at: 2018-10-22 17:18:51 UTC  
> Url: https://github.com/boostorg/tokenizer/pull/10  

PR fixing issue: https://github.com/boostorg/tokenizer/issues/9  
  
(Quite new to submitting PR, let me know if anything is wrong)

---

## Comment 1

> Username: jeking3  
> Created_at: 2018-10-22 22:40:07 UTC  
> Url: https://github.com/boostorg/tokenizer/pull/10#issuecomment-432016241  

Did this run CI builds?  They should be configured but I see no status.

---

## Comment 2

> Username: eldiener  
> Created_at: 2018-10-22 22:49:39 UTC  
> Url: https://github.com/boostorg/tokenizer/pull/10#issuecomment-432018499  

I tested it locally. I have not seen CI builds for awhile in any changed library.

---

## Comment 3

> Username: jeking3  
> Created_at: 2018-10-22 22:56:58 UTC  
> Url: https://github.com/boostorg/tokenizer/pull/10#issuecomment-432020288  

I just toggled the Travis and Appveyor CI builds off and on.  CI should run on PRs.  
  
Travis: PASS https://travis-ci.org/boostorg/tokenizer/builds/444727886  
AppVeyor: FAIL https://ci.appveyor.com/project/jeking3/tokenizer-c6pnd/builds/19694861  
  
Need to see if it's environmental.

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-10-22 22:58:10 UTC  
> Url: https://github.com/boostorg/tokenizer/pull/10#issuecomment-432020578  

Re-running AppVeyor: https://ci.appveyor.com/project/jeking3/tokenizer-c6pnd/builds/19702415

---
