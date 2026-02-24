# #280 - Replace Boost.MPL with Boost.MP11 in promote_integral.hpp [Closed]

> Username: mloskot  
> Created at: 2019-04-14 20:08:55 UTC  
> Updated at: 2020-03-10 18:47:06 UTC  
> Closed at: 2020-03-10 18:46:07 UTC  
> Url: https://github.com/boostorg/gil/issues/280  

Additional task related to #274 to switch the promoter implementation to Boost.MP11 and C++11

---

## Comment 1

> Username: jatinAroraGit  
> Created at: 2019-10-01 05:41:16 UTC  
> Url: https://github.com/boostorg/gil/issues/280#issuecomment-536874603  

Hey , Is this still in progress ? Can I work on this.

---

## Comment 2

> Username: mloskot  
> Created at: 2019-10-01 05:59:41 UTC  
> Url: https://github.com/boostorg/gil/issues/280#issuecomment-536879658  

AFAIK, it hasn't been started, so not in progress. You are very welcome to pick it up and submit a PR. Thanks!

---

## Comment 3

> Username: siddhant2001  
> Created at: 2019-11-27 19:47:12 UTC  
> Url: https://github.com/boostorg/gil/issues/280#issuecomment-559228395  

Any updates on progress? Can I work on this if available?

---

## Comment 4

> Username: mloskot  
> Created at: 2019-11-27 20:26:20 UTC  
> Url: https://github.com/boostorg/gil/issues/280#issuecomment-559239910  

@siddhant2001 AFAIK, nobody works on this, nobody from the GIL maintainers for sure. Please,  feel very welcome to pick it up and submit PR.

---

## Comment 5

> Username: siddhant2001  
> Created at: 2019-11-27 20:48:52 UTC  
> Url: https://github.com/boostorg/gil/issues/280#issuecomment-559246177  

@mloskot Is changing every instance of MPL to MP11 the only requirement?

---

## Comment 6

> Username: mloskot  
> Created at: 2019-11-27 23:19:19 UTC  
> Url: https://github.com/boostorg/gil/issues/280#issuecomment-559282912  

@siddhant2001 well, it's not automatic. If it was about search & replace `mpl` with `mp11`, then there would be no issue, would it.  
  
The procedure is like this:  
- replace `<boost/mpl*>` headers with `<boost/mp11.hpp>`  
- make source compile by finding and replacing MPL types  with MP11 equivalents  (eg. MPL vector with `mp_list`) and  same for metafunctions  
- once it compiles run tests and if tests do not pass, then debug it to figure out why, then change code to achieve MPL equivalent behaviour using MP11.   
  
Once it compiles and all tests pass (using single C++11 compiler of your choice is fine), it is ready to submit as pull request.

---

## Comment 7

> Username: parthsarthi1  
> Created at: 2020-01-06 06:25:12 UTC  
> Url: https://github.com/boostorg/gil/issues/280#issuecomment-571020870  

@mloskot Can I look over the issue and work on this if there's no development going on ?Thanks.

---

## Comment 8

> Username: mloskot  
> Created at: 2020-01-06 11:50:25 UTC  
> Url: https://github.com/boostorg/gil/issues/280#issuecomment-571111096  

@parthsarthi1 we haven't received any PRs for this. So, we still need help with this.

---

## Comment 9

> Username: HridayRaj  
> Created at: 2020-02-02 14:39:01 UTC  
> Url: https://github.com/boostorg/gil/issues/280#issuecomment-581141755  

@mloskot  Can I look over the issue and work on this if there`s no development going on ?

---

## Comment 10

> Username: mloskot  
> Created at: 2020-02-02 15:32:32 UTC  
> Url: https://github.com/boostorg/gil/issues/280#issuecomment-581146590  

@HridayRaj I'm not coordinating or scheduling contributors.   
  
If an issue is still open and any PRs submitted to fix an issue have not been approved, it means the issue remains open and everyone is free to propose PR with solution. If there is more than one PRs proposed, we can decide to pick optimal one.

---

## Comment 11

> Username: codejaeger  
> Created at: 2020-03-03 18:38:43 UTC  
> Updated at: 2020-03-03 21:15:45 UTC  
> Url: https://github.com/boostorg/gil/issues/280#issuecomment-594102658  

@mloskot , This issue already has a PR with respect to it, but since it is also mentioned as the bonus project for GSoC I wished to give it a try . From the [mp11](https://www.boost.org/doc/libs/develop/libs/mp11/doc/html/mp11.html#reference) it seemed the functionality of a type list iterator is no longer in (mp11) , but a meta function in mp11 mp_rest would do the job just fine. So does the (pseudo)meta function seem valid for what a iterator could do:-  
```  
//trying to come up with a PR implementing the above  
template<typename list>  
struct promote{};  
```  
I am stuck on this for some time now , and was forced to ask for some help. I will reproduce this in mpl's mailing list or channel if asked to do so. Thanks.

---

## Comment 12

> Username: mloskot  
> Created at: 2020-03-03 18:59:10 UTC  
> Url: https://github.com/boostorg/gil/issues/280#issuecomment-594112092  

@codejaeger   
> since it is also mentioned as the bonus project for GSoC  
  
This is not a GSoC project. It could be part of competency test though.  
  
> I wished to give it a try  
  
You do not to ask for permission or synchronise work with anyone.   
You're free to try, to work on it, to submit PR whenever you have anything you wish to to contribute. I can promise to review any PR. If a PR is good, it has high chance of being merged.  
  
> meta function in mp11 mp_rest would do the job just fine  
  
Sure, number of techniques is possible, even hand-rolled `mp_cond`-itions as set of types is minimal. See https://github.com/boostorg/gil/pull/422#issuecomment-582811665

---

## Comment 13

> Username: codejaeger  
> Created at: 2020-03-03 20:21:56 UTC  
> Updated at: 2020-03-03 20:22:47 UTC  
> Url: https://github.com/boostorg/gil/issues/280#issuecomment-594149595  

@mloskot I might have hid the main reason of the comment behind some foolish statements XD,  
What I was hoping to get answered to was whether the above pseudo code(indented it for better readability) might lead to any obvious errors . I wouldn't ask help debugging my code , but I already went through the mp11 docs, but to no avail. I will also look into the C++ Template MetaProgramming book for some help.  
Is there any way of posting the same as an mp11 "issue" other than the mailing list?

---

## Comment 14

> Username: mloskot  
> Created at: 2020-03-03 20:25:46 UTC  
> Url: https://github.com/boostorg/gil/issues/280#issuecomment-594151117  

There is a test available for `promote_integral`, just use it (or re-write equivalent) and start testing/debugging from there. As soon as your code passes the existing set of test cases, it is ready for review. If your code does not pass those tests, then reviewing it is a waste of time, I'm afraid.  
  
> Is there any way of posting the same as an mp11 "issue" other than the mailing list?  
  
You can ask any question of MP11 usage on https://lists.boost.org/boost-users/ just start subject with `[mp11]` to make it easier to spot by those skilled in MP11.

---

## Comment 15

> Username: codejaeger  
> Created at: 2020-03-03 20:41:40 UTC  
> Url: https://github.com/boostorg/gil/issues/280#issuecomment-594157999  

>  If your code does not pass those tests, then reviewing it is a waste of time, I'm afraid.  
  
Yeah , I get it . Thanks, btw.
