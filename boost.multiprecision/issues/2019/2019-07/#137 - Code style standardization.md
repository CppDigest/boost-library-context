# #137 - Code style standardization [Closed]

> Username: nemothenoone  
> Created at: 2019-07-13 17:03:07 UTC  
> Updated at: 2019-08-05 08:34:49 UTC  
> Closed at: 2019-08-05 08:34:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/137  

Are there any code style standard the developer should keep close to?  
  
I mean I'm going to bring a lot of changes in Boost.Multprecision, so we need to agree on some code style.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-07-14 07:55:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/137#issuecomment-511181830  

I suppose I should really write a .clang.format file for the library!  
  
I'm not going to get on too much of a bikeshed discussion over style, the most important thing is to keep the code well commented and easy to read and understand.  I personally prefer well spaced out code to dense/terse formatting as you'll notice from the existing code, but otherwise I hope I'm not overly fussy.  
  
Oh and make any PR's so I can see what's changed - which is to say please don't mix up formatting changes with substantive changes if you can help it.  
  
What changes were you planning?  The next big change I want to get back to is full constexpr support - I did a fair bit of work on this last year (https://github.com/boostorg/multiprecision/pull/84) but the effort stalled due to a lack of `std::is_constant_evaluated`.  Now that we are starting to see support for that I need to revisit that..... I do need to finish off hypergeometrics for Boost.Math first though ;)

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-07-14 10:53:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/137#issuecomment-511192543  

I've added a .clang-format file: it makes a lot of busy-body changes to existing code, but in the same spirit as what's there already (I hope!).

---

## Comment 3

> Username: nemothenoone  
> Created at: 2019-07-14 12:13:29 UTC  
> Updated at: 2019-07-14 13:51:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/137#issuecomment-511197635  

That is totally great! I'm gonna automatically reformat the code to be compliant to the .clang_format introduced and come back with some pull request. It makes some minor changes to spacing inside various operator definitions and to braces emplacement, but this makes no big change.  
  
Such a pull request intention is to adjust the ```.clang_format``` statements.

---

## Comment 4

> Username: nemothenoone  
> Created at: 2019-07-14 12:27:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/137#issuecomment-511198529  

Coming to the full ```constexpr``` support, so I think, I would need to take this in sight, because my changes are all about underlying algorithms. I can make a monster-issue listing all the changes I want to bring, or I can create issue-by-issue along with pull requests with stuff I want to merge in.   
  
What would be the most convenient way to you?

---

## Comment 5

> Username: jzmaddock  
> Created at: 2019-07-14 13:54:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/137#issuecomment-511204852  

If you can avoid monster PR's that would be appreciated - it makes it really hard to figure out what's going on ;)

---

## Comment 6

> Username: nemothenoone  
> Created at: 2019-07-14 13:55:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/137#issuecomment-511204913  

No, it is not about the monster PR, but about monster issue simply listing the upcoming changes.

---

## Comment 7

> Username: nemothenoone  
> Created at: 2019-08-05 08:34:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/137#issuecomment-518140332  

Since #142 is merged, I think this is not relevant anymore.
