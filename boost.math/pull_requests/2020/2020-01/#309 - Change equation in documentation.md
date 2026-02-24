# #309 Change equation in documentation [Merged]

> Username: egarpor  
> Created at: 2020-01-23 21:41:36 UTC  
> Updated at: 2020-01-26 13:15:59 UTC  
> Merged at: 2020-01-24 13:11:01 UTC  
> Closed at: 2020-01-24 13:11:01 UTC  
> Url: https://github.com/boostorg/math/pull/309  

I think the documentation of `hypergeometric_pFq` contains a typo. To match the usual definition (see, e.g. [here](http://dlmf.nist.gov/16.2.E1) or [here](https://en.wikipedia.org/wiki/Generalized_hypergeometric_function#Notation)) of the generalized hypergeometric function, the equation   
  
![hypergeometric_pfq_1](https://user-images.githubusercontent.com/1271286/73025394-cf44d080-3e2f-11ea-92d4-fa217d7f3d6b.png)  
  
should read as  
  
![123e9e99eb214073df0f610c2a501a4c](https://user-images.githubusercontent.com/1271286/73026174-38791380-3e31-11ea-8515-41edfb43d862.png)  
  
The LaTeX code for the latter equation is  
  
```  
{}_pF_q\left(\left\{a_1, \ldots, a_p\right\}, \left\{b_1, \ldots, b_q\right\}; z\right)=\sum_{n=0}^{\infty} \frac{\prod_{j=1}^{p}\left(a_j\right)_n}{\prod_{j=1}^{q}\left(b_{j}\right)_n} \frac{z^n}{n!}  
```  
  
I did the conversion from LaTeX to svg using [this tool](https://viereck.ch/latex-to-svg/)).

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2020-01-23 21:50:09 UTC  
> Url: https://github.com/boostorg/math/pull/309#issuecomment-577893502  

> I think the documentation of hypergeometric_pFq contains a typo.   
  
Ahhh... yes. Thank youi. I think you are pointing out two differences.  
* {a} and {b} can have different numbers of coefficients.  
* Use the index parameter consistently in the summation  
  
Is this interpretation of your message correct?

---

## Comment 2

> Username: egarpor  
> Created_at: 2020-01-23 22:09:14 UTC  
> Updated_at: 2020-01-23 22:11:04 UTC  
> Url: https://github.com/boostorg/math/pull/309#issuecomment-577900860  

Yes, precisely:  
  
* {a} and {b} have p and q parameters  
* The index n was incorrectly used in the Pochamer and product symbols  
  
As another minor thing, I also put commas to separate the list of parameters in {a} and {b} to avoid thinking they are a product.  
  
Thanks!

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2020-01-23 22:22:56 UTC  
> Updated_at: 2020-01-23 22:23:08 UTC  
> Url: https://github.com/boostorg/math/pull/309#issuecomment-577906005  

> documentation of hypergeometric_pFq contains a typo...  
  
Yes. Indeed.  
  
> I did the conversion from LaTeX to svg...  
  
The changes look good and technically correct. I notice that your svg has a vertical format, is a bit easier to read, but I think this stylistic change should be reviewed by one of the original authors of that equation.

---

## Comment 4

> Username: egarpor  
> Created_at: 2020-01-23 22:38:07 UTC  
> Url: https://github.com/boostorg/math/pull/309#issuecomment-577911034  

> I notice that your svg has a vertical format, is a bit easier to read, but I think this stylistic change should be reviewed by one of the original authors of that equation.  
  
Sure! I tried to mimic the style, but indeed there is some discrepancy.  
  
Actually, when trying to fix what I believe is another typo in the same help page (the single hit of `m_type` should be `mp_type` -- otherwise `m_type` is not defined) I realized that it was possible to edit the [`hypergeometric.qbk`](https://github.com/boostorg/math/blob/a716b525ef19272a8ff49323436c2117e52ce538/doc/sf/hypergeometric.qbk) to add there the new equation (which I guess it would generate the equation in the same way as the original was generated).  
  
I can open a new PR (and close this one) with these changes in the hypergeometric.qbk if that is cleaner.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2020-01-23 22:42:42 UTC  
> Url: https://github.com/boostorg/math/pull/309#issuecomment-577912386  

> believe is another typo in the same help page  
  
> I can open a new PR (and close this one) with these changes in the hypergeometric.qbk if that is cleaner.  
  
Thank you for pursuing this.  
  
I think perhaps this is worth opening an issue. When qbk docs are modified, they must also be built and checked. That is getting into the realm of kind of needing an issue, tracking and ultimately a branch.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2020-01-24 13:15:06 UTC  
> Url: https://github.com/boostorg/math/pull/309#issuecomment-578125175  

As the person responsible for the typo: many thanks for spotting that!  The revised equation is fine and I've gone ahead and merged.  Also fixed the m_type->mp_type typo, and updated the latex in the comment to match yours.  
  
The latex on the documentation source are just comments BTW - they don't automatically generate the SVG's or anything, they're just there to make it easier to fix minor typos without starting the whole equation over again.  I used http://www.tlhiv.org/ltxpreview/ to generate the SVG's, but I suspect they use the same scripts underneath as the site you used.

---

## Comment 7

> Username: egarpor  
> Created_at: 2020-01-26 13:15:58 UTC  
> Url: https://github.com/boostorg/math/pull/309#issuecomment-578500874  

@jzmaddock thanks for adding the changes! I am glad they were helpful.  
  
Thanks also for letting me know that the LaTeX comments do not generate the equation -- I did not know very well how the generation of the documentation by `qtc` format works.

---
