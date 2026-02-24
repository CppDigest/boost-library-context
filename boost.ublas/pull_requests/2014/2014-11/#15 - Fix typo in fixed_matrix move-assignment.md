# #15 Fix typo in fixed_matrix move-assignment [Closed]

> Username: Houndie  
> Created at: 2014-11-06 15:24:59 UTC  
> Updated at: 2015-01-29 15:57:36 UTC  
> Closed at: 2015-01-14 09:01:06 UTC  
> Url: https://github.com/boostorg/ublas/pull/15  

The move assignment operator= had an operand of "matrix", which is an  
undefined type, causing a compile error.  Changing the type to "fixed_matrix"  
solves the problem.  
  
Since this is supposed to be a move-assignment operator, I made it take an  
r-value reference as well, which seems to be the general agreed-upon way  
to define this operator.  
  
This is related to trac bug #10762

---

## Comment 1

> Username: nasosi  
> Created_at: 2014-12-15 21:06:44 UTC  
> Url: https://github.com/boostorg/ublas/pull/15#issuecomment-67065742  

Houndle, can you please request the pull in https://github.com/uBLAS/ublas/tree/develop instead of boostorg/ublas?  
Thanks!

---

## Comment 2

> Username: yimyom  
> Created_at: 2015-01-14 09:01:06 UTC  
> Url: https://github.com/boostorg/ublas/pull/15#issuecomment-69886996  

can't accept pull request on the main branch. Can you please submit it to ublas/develop please.

---

## Comment 3

> Username: Houndie  
> Created_at: 2015-01-29 15:57:36 UTC  
> Url: https://github.com/boostorg/ublas/pull/15#issuecomment-72049037  

Whoops.  Sorry, for some reason github turned off notifications for me.  I'll work on this right now.

---
