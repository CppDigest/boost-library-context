# #638 - Typo in the documentation of the Hypergeometric Functions [Closed]

> Username: Noxbru  
> Created at: 2021-06-02 09:29:42 UTC  
> Updated at: 2021-06-16 16:56:12 UTC  
> Closed at: 2021-06-16 13:39:26 UTC  
> Url: https://github.com/boostorg/math/issues/638  

Hi, I was checking the documentation for the implementation of the 1F1 hypergeometric function and found a typo in one of the formulas.  
  
https://github.com/boostorg/math/blob/develop/doc/sf/hypergeometric.qbk#L278 is incorrect.  
  
It currently reads: [:[/\large  $$_1F_1(a, b; -z) = e^{-z} {_1F_1}(a, b; z)$$]  
  
But should read: [:[/\large  $$_1F_1(a, b; -z) = e^{-z} {_1F_1}(b-a, b; z)$$]  
(note that the first parameter of the resulting 1F1 function is `b-a` instead of just `a`)  
  
Relevant link to DLMF: https://dlmf.nist.gov/13.2#E39

---

## Comment 1

> Username: pabristow  
> Created at: 2021-06-04 14:40:19 UTC  
> Url: https://github.com/boostorg/math/issues/638#issuecomment-854779922  

Thanks for noting this. Will be fixed with the kurtosis issue see https://github.com/boostorg/math/issues/639.

---

## Comment 2

> Username: pabristow  
> Created at: 2021-06-16 13:39:26 UTC  
> Url: https://github.com/boostorg/math/issues/638#issuecomment-862388998  

I don't have the Math ML software to edit and regenerate the SVG equations, but added a correction to the documentation in PR https://github.com/boostorg/math/pull/642.

---

## Comment 3

> Username: NAThompson  
> Created at: 2021-06-16 13:46:39 UTC  
> Url: https://github.com/boostorg/math/issues/638#issuecomment-862394891  

@pabristow : Try this: https://viereck.ch/latex-to-svg/

---

## Comment 4

> Username: pabristow  
> Created at: 2021-06-16 13:56:50 UTC  
> Url: https://github.com/boostorg/math/issues/638#issuecomment-862403121  

This is a much better way, and produces much smaller, nicer and editable SVG, but it would mean redoing all the equations, and I don't feel it is worth the time and effort, and risk of mistakes for this tiny and obscure change.  The now authoratitive DMLF link is provided.

---

## Comment 5

> Username: Noxbru  
> Created at: 2021-06-16 13:59:00 UTC  
> Url: https://github.com/boostorg/math/issues/638#issuecomment-862404885  

To complement on what @NAThompson added, I have \latex installed and can produce the SVGs if necessary. Also, I've seen in the documentation that some regions of the parameter space for 1F1 are difficult to calculate accurately. I really like tinkering with numerics and could spend some time trying to find a way of making those regions more accurate, but I would need a bit of help with setting up boost.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2021-06-16 15:09:22 UTC  
> Url: https://github.com/boostorg/math/issues/638#issuecomment-862462434  

I think it's easier with the equation in the docs, I've been using http://www.tlhiv.org/ltxpreview/ and then "archiving" the latex used as a comment in the doc so it's easier to correct errors in future.  You can make the equations a more sensible size with `\large`, so for example:  
  
```  
\Large  $$ {}_pF_q\left(\left\{a_1, \ldots, a_p\right\}, \left\{b_1, \ldots, b_q\right\}; z\right)=\sum_{n=0}^{\infty} \frac{\prod_{j=1}^{p}\left(a_j\right)_n}{\prod_{j=1}^{q}\left(b_{j}\right)_n} \frac{z^n}{n!} $$  
```  
  
Was the last equation I did that way.

---

## Comment 7

> Username: jzmaddock  
> Created at: 2021-06-16 16:56:12 UTC  
> Url: https://github.com/boostorg/math/issues/638#issuecomment-862548272  

>Also, I've seen in the documentation that some regions of the parameter space for 1F1 are difficult to calculate accurately. I really like tinkering with numerics and could spend some time trying to find a way of making those regions more accurate, but I would need a bit of help with setting up boost.  
  
Just download it :)  We can help with running tests later.  
  
With regard to the difficult regions of 1F1 - I don't think anyone has solved those yet - in fact I don't even think anyone had documented them until now :(  
  
They niggle me as well though, so if you want to investigate, please do so!  :)  
  
The basic issue is this - there is a "valley of death" where the function either passes through zero or goes to a minima (depending on whether floor(b) is odd or even).  Normally you can use MIllers method, as long as you're iterating away from the valley floor (ie the direction of stability), but if you're hard up against the minima/zero then there is no stable direction :(  Now to be fair, the result in these regions is so small  - smaller than anything a double could possibly represent say - that we could just return zero and be done with it.  However, you're quite often combining the result with something else super-large to generate a finite result - 1F1 in another domain maybe - so there are actually good reasons for wanting a valid result even when it's super small/large.  
  
There are BTW, some novel recurrence relations for 1F1 here: https://github.com/boostorg/math/blob/45a2cbe789e1b81d310024d58c9616cc434a63d3/include/boost/math/special_functions/detail/hypergeometric_1F1_recurrence.hpp#L138, which are all trivially derived from the other well-known relations.  This one: https://github.com/boostorg/math/blob/45a2cbe789e1b81d310024d58c9616cc434a63d3/include/boost/math/special_functions/detail/hypergeometric_1F1_recurrence.hpp#L245  should in theory traverse more or less along the "valley side" rather than towards/away from the problem zone, unfortunately I haven't been able to make numerically stable enough to work :(  
  
Anyhow, you will find that 1F1 is a really stubborn beast of a function that's also really quite engaging/interesting to investigate.  
  
Probably best to open a fresh issue to discuss 1F1 if you decide to investigate further, I can try and look out some problem input values for you as well.
