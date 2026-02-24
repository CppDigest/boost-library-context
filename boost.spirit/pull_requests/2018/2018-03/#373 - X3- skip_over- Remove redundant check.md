# #373 X3: skip_over: Remove redundant check [Merged]

> Username: Kojoley  
> Created at: 2018-03-06 17:39:04 UTC  
> Updated at: 2018-10-10 12:26:33 UTC  
> Merged at: 2018-03-11 14:38:53 UTC  
> Closed at: 2018-03-11 14:38:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/373  

End iterator check is redundant here because it is done in skipper.

---

## Comment 1

> Username: djowel  
> Created_at: 2018-03-06 21:02:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/373#issuecomment-370926994  

I think we should at least have a precondition assert there to be sure.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2018-03-06 21:10:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/373#issuecomment-370929395  

We can't place an assertion here, it will fire when postskip is enabled and there is nothing to postskip.

---

## Comment 3

> Username: djowel  
> Created_at: 2018-03-06 22:49:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/373#issuecomment-370957429  

> We can't place an assertion here, it will fire when postskip is enabled and there is nothing to postskip.  
  
We can add the check in the postskip so it won't fire.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2018-03-06 22:55:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/373#issuecomment-370958847  

I don't know how it can be done without touching everything else, and it will still fire if `eps` or `attr` is the last parser in a sequence.

---

## Comment 5

> Username: djowel  
> Created_at: 2018-03-06 22:56:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/373#issuecomment-370959273  

> I don't know how it can be done without touching everything else, and it will still fire if eps or attr is the last parser in a sequence.  
  
Yeah, thinking about it some more, I'm coming to the same conclusion.

---

## Comment 6

> Username: djowel  
> Created_at: 2018-03-06 23:00:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/373#issuecomment-370960101  

OK, you are right. It should not be done here.

---

## Comment 7

> Username: breuhan  
> Created_at: 2018-10-10 12:19:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/373#issuecomment-428549927  

Attention this change is introducing a regression in boost 1.68.0 and 1.67.0 if you use something like (as shown in some example code):  
    phrase_parse(begin, end, rule, !byte);  
The program than hangs in an infinite loop. I was able to fix that by using   
    phrase_parse(begin, end, rule, !eps);

---

## Comment 8

> Username: Kojoley  
> Created_at: 2018-10-10 12:26:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/373#issuecomment-428551855  

@cylonbrain please provide a minimal working example. If you think there is a regression it would be better to open an issue.

---
