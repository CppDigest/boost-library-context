# #200 vector: Simplify assign_sequence [Merged]

> Username: Kojoley  
> Created at: 2018-10-10 00:36:02 UTC  
> Updated at: 2022-09-09 22:23:38 UTC  
> Merged at: 2021-09-04 06:16:31 UTC  
> Closed at: 2021-09-04 06:16:31 UTC  
> Url: https://github.com/boostorg/fusion/pull/200  



---

## Comment 1

> Username: djowel  
> Created_at: 2018-10-10 03:13:27 UTC  
> Url: https://github.com/boostorg/fusion/pull/200#issuecomment-428423619  

Cool @Kojoley !

---

## Comment 2

> Username: vtnerd  
> Created_at: 2018-10-15 20:25:56 UTC  
> Updated_at: 2018-10-15 20:26:18 UTC  
> Url: https://github.com/boostorg/fusion/pull/200#issuecomment-430000502  

Agreed (with @djowel ), this should reduce compile times on larger sequences.

---

## Comment 3

> Username: Flast  
> Created_at: 2018-10-17 23:09:51 UTC  
> Url: https://github.com/boostorg/fusion/pull/200#issuecomment-430820469  

Does fold expr version have any advantage over c++11 version?

---

## Comment 4

> Username: Kojoley  
> Created_at: 2018-10-17 23:25:50 UTC  
> Url: https://github.com/boostorg/fusion/pull/200#issuecomment-430823523  

Perhaps only in debug binaries.

---

## Comment 5

> Username: Flast  
> Created_at: 2018-10-25 15:11:26 UTC  
> Updated_at: 2018-11-01 11:50:14 UTC  
> Url: https://github.com/boostorg/fusion/pull/200#issuecomment-433089977  

Hm... I got bad compile time result with GCC 8.  
  
https://gist.github.com/Flast/5d162d339453113c690e9386dc9205f2/2ffe61615b94b7d3cc46280e0aeceb58d2232a24

---

## Comment 6

> Username: Kojoley  
> Created_at: 2018-10-25 15:16:08 UTC  
> Url: https://github.com/boostorg/fusion/pull/200#issuecomment-433091764  

Are the results persistent. How many runs you did?

---

## Comment 7

> Username: Kojoley  
> Created_at: 2018-10-25 15:48:31 UTC  
> Url: https://github.com/boostorg/fusion/pull/200#issuecomment-433104267  

My results on GCC 8.2.0 is a bit different, `-DBOOST_NO_CXX17_FOLD_EXPRESSIONS` is about 3% faster to compile, though I have reduced number of arguments from 500 to 256 because I do not have enough memory.

---

## Comment 8

> Username: Flast  
> Created_at: 2018-10-26 11:07:16 UTC  
> Url: https://github.com/boostorg/fusion/pull/200#issuecomment-433372692  

> Are the results persistent. How many runs you did?  
  
I run two or three times and paste worst case.  
  
> `-DBOOST_NO_CXX17_FOLD_EXPRESSIONS` is about 3% faster to compile,  
  
Is your case faster than bose of current and fold expr? or which?

---

## Comment 9

> Username: Kojoley  
> Created_at: 2018-10-26 11:17:10 UTC  
> Updated_at: 2018-10-26 11:17:34 UTC  
> Url: https://github.com/boostorg/fusion/pull/200#issuecomment-433374767  

> paste worst case  
  
That was not a good decision. It could be anything that disrupted the result :-)  
  
> Is your case faster than bose of current and fold expr? or which?  
  
secs | commit  
---- | ------  
25.8 | develop  
16.3 | this patch  
15.5 | this patch with `-DBOOST_NO_CXX17_FOLD_EXPRESSIONS`  
  
best of 5 runs

---

## Comment 10

> Username: Flast  
> Created_at: 2018-10-26 17:13:56 UTC  
> Url: https://github.com/boostorg/fusion/pull/200#issuecomment-433479606  

>  > paste worst case  
>  
> That was not a good decision. It could be anything that disrupted the result :-)  
  
But it showed a similar trend even though best case...

---

## Comment 11

> Username: Kojoley  
> Created_at: 2018-10-26 17:19:53 UTC  
> Url: https://github.com/boostorg/fusion/pull/200#issuecomment-433481289  

> But it showed a similar trend even though best case...  
  
I cannot say that 205/498/259 is similar to 258/163/155

---

## Comment 12

> Username: Flast  
> Created_at: 2018-10-28 05:30:06 UTC  
> Updated_at: 2018-10-28 05:30:13 UTC  
> Url: https://github.com/boostorg/fusion/pull/200#issuecomment-433677817  

> > But it showed a similar trend even though best case...  
>   
> I cannot say that 205/498/259 is similar to 258/163/155  
  
I meant that, in my case, bose of best and worst cases are showed a similar trend.

---

## Comment 13

> Username: Kojoley  
> Created_at: 2019-04-20 15:37:33 UTC  
> Url: https://github.com/boostorg/fusion/pull/200#issuecomment-485136643  

Ping

---
