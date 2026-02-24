# #190 Quad double: Add initial/tentative support for libqd. [Open]

> Username: jzmaddock  
> Created at: 2020-01-29 18:58:44 UTC  
> Updated at: 2021-12-22 18:36:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/190  

See https://github.com/boostorg/multiprecision/issues/184.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2020-01-29 21:15:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/190#issuecomment-579963181  

> See #184.  
  
Awesome ! I was thinking you might start moving forward on this. Looks like a promising start...

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2021-02-23 21:48:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/190#issuecomment-784535992  

I would like to refresh interest in this project (and can mentor some additional potential progress on it) for [Boost Google Summer of Code 2021](https://github.com/boostorg/wiki/wiki/Google-Summer-of-Code:-2021)

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-02-24 10:28:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/190#issuecomment-784975502  

SOC is a good thought, the code that's here works, needs more testing (there's one unresolved issue I think), and if possible it would be great to move this to C++ only (ie without the external library).

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2021-02-24 18:34:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/190#issuecomment-785284980  

> SOC is a good thought, the code that's here works, needs more testing (there's one unresolved issue I think), and if possible it would be great to move this to C++ only (ie without the external library).  
  
Agreed... on all points. I will comply in that order.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2021-02-24 19:51:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/190#issuecomment-785330674  

I tried a big merge operation of today's develop onto this relatively old branch.

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2021-04-20 09:43:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/190#issuecomment-823138080  

Develop has been successfully merged into this branch. This is good and partially prepares the road for further work on this topic. If (or when) I personally do something on QD, I think we should simply use (re-use, continue to use) this PR.  
  
Is that OK to keep going on QD on this PR, at least at first for a while?

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2021-04-20 12:07:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/190#issuecomment-823220246  

Good question, I guess if it's a GSOC project then it might be easier to fork this off into the student's own repro?

---

## Comment 8

> Username: AuroraDysis  
> Created_at: 2021-04-20 12:18:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/190#issuecomment-823226785  

I tried to use qd in my program several times, but according to my experience, it has a lot of bugs. I dare say that I am not alone and there are others who experienced a similar situation. For example  
  
https://github.com/ddmarshall/Code-Eli/issues/27#issue-23182272

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2021-04-20 20:56:04 UTC  
> Updated_at: 2021-04-20 20:59:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/190#issuecomment-823595038  

> tried to use qd in my program several times, but according to my experience, it has a lot of bugs.   
  
Thank you for the heads up.  
  
The intent of integrating this QD thing is to either use it (if it's good enough out of the box) or replacing entirely its algorithms under Boost licensing if it's not. At the moment, we have not fully or exhaustively estimated the work/effort in these activities, but the potential GSoC is expected to move this endeavor forward.

---

## Comment 10

> Username: anilmuthigi  
> Created_at: 2021-05-11 00:35:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/190#issuecomment-837553708  

I was going through this function :   
  
inline void eval_divide(quad_double_backend& result, const quad_double_backend& a, const quad_double_backend& b)  
 {  
    bool i1 = (boost::math::isinf)(a.data()[0]);  
    bool i2 = (boost::math::isinf)(b.data()[0]);  
    if (i1 && !i2)  
       result.data() = a.data();  
    else if (i2 && !i1)  
       result.data() = 0.0;  
    else  
       result.data() = a.data() / b.data();  
 }  
   
 Don't you think that b.data()=0.0 should be handled separately?

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2021-05-11 07:59:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/190#issuecomment-838026406  

>Don't you think that b.data()=0.0 should be handled separately?  
  
I guess only if the division doesn't generate an infinity anyway - and I'm assuming it does?

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2021-05-11 10:00:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/190#issuecomment-838194515  

> going through this function  
  
Yes. Thanks for looking.  
  
There are a whole bunch of functions in this kind of multiprecision adaption. As this project moves on, we will be looking into this function and numerous others, re-writing some, adaptimg others, all with the common goal of getting an efficient, portable, reliable, tested QD running.  
  
I think we wil luncover a bunch of little nuances like this one...

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2021-05-11 11:31:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/190#issuecomment-838312164  

>I think we wil luncover a bunch of little nuances like this one...  
  
Indeed, what I should have said was: if the special case is already handled by the hardware, then we should leave it to it on the grounds that it will be way more efficient than any code we can write.

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2021-05-11 11:39:06 UTC  
> Updated_at: 2021-05-11 11:39:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/190#issuecomment-838322185  

> if the special case is already handled by the hardware  
  
Yes John. Agreed. That's actually a detail that I'll be asking you about/discussing with you, regarding your advice and best way to/or not to do it, later in the project.

---
