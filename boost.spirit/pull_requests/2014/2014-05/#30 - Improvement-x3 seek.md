# #30 Improvement/x3 seek [Merged]

> Username: vtnerd  
> Created at: 2014-05-10 21:03:01 UTC  
> Updated at: 2021-05-18 20:14:41 UTC  
> Merged at: 2014-05-10 22:46:26 UTC  
> Closed at: 2014-05-10 22:46:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/30  

Slight modification to x3-seek implementation after K-ballo's feedback.

---

## Comment 1

> Username: K-ballo  
> Created_at: 2014-05-10 21:29:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/30#discussion_r12506290  

If we get out here then `current == last`. Do you think we should make it clear by using `last` instead of `current` (and moving the definition of `current` into the _init-statement_)? I'm unsure about this myself, so I'll leave it up to you.

---

## Comment 2

> Username: vtnerd  
> Created_at: 2014-05-10 22:40:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/30#discussion_r12506634  

I thought about that too. The first argument to `this->subject.parse` is a mutable l-value, and `last` is a const reference. I suppose I could take the incoming `last` argument by copy, but thats likely to look like a mistake too.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2021-05-18 20:14:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/30#issuecomment-843523954  

Does anyone remember what this PR had been fixing? It introduced a regression #658. I cannot come to a reproducer from the changes. It also diverged X3 seek implementation from Qi.

---
