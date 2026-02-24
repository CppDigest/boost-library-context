# #20 Fix typo in fixed_matrix move-assignment [Merged]

> Username: Houndie  
> Created at: 2015-01-29 16:05:32 UTC  
> Updated at: 2015-04-20 14:45:09 UTC  
> Merged at: 2015-03-14 15:14:22 UTC  
> Closed at: 2015-03-14 15:14:22 UTC  
> Url: https://github.com/boostorg/ublas/pull/20  

The move assignment operator= had an operand of "matrix", which is an  
undefined type, causing a compile error.  Changing the type to "fixed_matrix"  
solves the problem.  
  
Since this is supposed to be a move-assignment operator, I made it take an  
r-value reference as well, which seems to be the general agreed-upon way  
to define this operator.  
  
This is related to trac bug #10762

---

## Comment 1

> Username: Houndie  
> Created_at: 2015-01-29 16:06:16 UTC  
> Url: https://github.com/boostorg/ublas/pull/20#issuecomment-72050729  

Resubmitting the pull-request on hopefully the correct branch this time.

---

## Comment 2

> Username: yimyom  
> Created_at: 2015-03-12 11:40:53 UTC  
> Url: https://github.com/boostorg/ublas/pull/20#issuecomment-78464800  

Hi, could you please resubmit any change you want to https://github.com/uBLAS/ublas  
No pull request will be accepted when submitted to the main boostorg repository.  
Many thanks

---

## Comment 3

> Username: Houndie  
> Created_at: 2015-04-17 14:19:27 UTC  
> Url: https://github.com/boostorg/ublas/pull/20#issuecomment-93994986  

Hey @yimyom do you still need me to resubmit this change to the other repository?  I wasn't sure since you accepted the pull.

---

## Comment 4

> Username: yimyom  
> Created_at: 2015-04-19 15:53:47 UTC  
> Url: https://github.com/boostorg/ublas/pull/20#issuecomment-94290294  

yes indeed. I clicked too fast. I should in fact only accept pull request  
on ublas:develop. No worries  
  
On Fri, Apr 17, 2015 at 3:19 PM, Houndie notifications@github.com wrote:  
  
> Hey @yimyom https://github.com/yimyom do you still need me to resubmit  
> this change to the other repository? I wasn't sure since you accepted the  
> pull.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/ublas/pull/20#issuecomment-93994986.

---

## Comment 5

> Username: Houndie  
> Created_at: 2015-04-20 14:45:09 UTC  
> Url: https://github.com/boostorg/ublas/pull/20#issuecomment-94471808  

Done https://github.com/uBLAS/ublas/pull/29

---
