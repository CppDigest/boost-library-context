# #357 - Concepts for set-theoretic functions [Open]

> Username: shreyans800755  
> Created at: 2017-07-01 08:17:49 UTC  
> Updated at: 2017-07-03 19:54:56 UTC  
> Url: https://github.com/boostorg/hana/issues/357  

At the moment, there are no concepts written for set-theoretic functions, due to which minor errors lengthens up to multiple pages unnecessarily.

---

## Comment 1

> Username: shreyans800755  
> Created at: 2017-07-01 08:18:13 UTC  
> Url: https://github.com/boostorg/hana/issues/357#issuecomment-312418674  

Working on issue, will create PR as soon as considerable work is done.

---

## Comment 2

> Username: ldionne  
> Created at: 2017-07-01 17:24:47 UTC  
> Url: https://github.com/boostorg/hana/issues/357#issuecomment-312444681  

Awesome! Thanks a lot! Feel free to submit a PR with whatever you've got so that we can iterate on the wording of the concepts, which I expect will take a few iterations to get right.

---

## Comment 3

> Username: shreyans800755  
> Created at: 2017-07-01 21:31:16 UTC  
> Url: https://github.com/boostorg/hana/issues/357#issuecomment-312456648  

@ldionne Sure. Just want to clarify: Do we need different commits for different set-theoretic-functions or single ?

---

## Comment 4

> Username: ldionne  
> Created at: 2017-07-01 21:44:02 UTC  
> Url: https://github.com/boostorg/hana/issues/357#issuecomment-312457184  

Not sure I understand. What I refer to when I say concepts is adding a new concept like `Monad` or `Foldable`, but suitable for set-theoretic operations. That should not require changing the existing functions except in a very mechanical way. Either one or many commits is fine, I don't have a preference so long as the PR is convenient to review.

---

## Comment 5

> Username: shreyans800755  
> Created at: 2017-07-03 19:27:25 UTC  
> Url: https://github.com/boostorg/hana/issues/357#issuecomment-312718899  

@ldionne Are we looking for different concepts for different set-theoretic functions, or just a single one for all ?

---

## Comment 6

> Username: ldionne  
> Created at: 2017-07-03 19:54:56 UTC  
> Url: https://github.com/boostorg/hana/issues/357#issuecomment-312722954  

Ideally a single one for all. Just like `Functor` has `transform`, `replace_if`, `replace`, etc.., I am looking for some `SetTheoretic` concept (name TBD) that has `difference`, `union`, `intersection`, etc...
