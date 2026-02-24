# #275 Fix typos [Merged]

> Username: bwignall  
> Created at: 2019-12-01 13:06:39 UTC  
> Updated at: 2019-12-06 09:21:42 UTC  
> Merged at: 2019-12-03 17:21:36 UTC  
> Closed at: 2019-12-03 17:21:36 UTC  
> Url: https://github.com/boostorg/math/pull/275  

Uses https://en.wikipedia.org/wiki/Wikipedia:Lists_of_common_misspellings/For_machines to find likely typos.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2019-12-01 14:46:34 UTC  
> Url: https://github.com/boostorg/math/pull/275#issuecomment-560116550  

Wow! Just took a look through these diffs and didn't see any that would screw stuff up. So pending a build I'm happy; but maybe a few a British English?

---

## Comment 2

> Username: bwignall  
> Created_at: 2019-12-01 15:01:45 UTC  
> Url: https://github.com/boostorg/math/pull/275#issuecomment-560118256  

@NAThompson , that's entirely possible. I took any entry in the Wikipedia page as a possible typo, without consideration for cross-country variations, and I naively took its suggestion as the preferred spelling.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2019-12-02 14:21:09 UTC  
> Url: https://github.com/boostorg/math/pull/275#issuecomment-560416348  

Ok, do you have script which does these changes? This looks nice to me, but it might also be helpful to apply it periodically in the future.

---

## Comment 4

> Username: pabristow  
> Created_at: 2019-12-02 17:03:31 UTC  
> Url: https://github.com/boostorg/math/pull/275#issuecomment-560486368  

I'm shocked - I've re-read all this several times and fixed dozens of typos :-(  
  
I'd like to use the script on the Multiprecision docs too as I am working on a revision of this too.  
  
(I will go though all the changed proposed and check them out.)

---

## Comment 5

> Username: bwignall  
> Created_at: 2019-12-02 17:16:43 UTC  
> Url: https://github.com/boostorg/math/pull/275#issuecomment-560491689  

@NAThompson , @pabristow : I will try to put up something by the end of the week. It's a simple (<100 LOC), naive, regex-based script, but it (semi-)automates this.

---

## Review 6 [Approved]

> Username: pabristow  
> Created_at: 2019-12-02 17:51:30 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/275#pullrequestreview-325420377  

These all look OK to me (and I note yet more Aberrant Apostrophes too :-( )  
  
Dyslexia Rules KO, aided and abetted by Copy'n'Paste!  
  
I must switch on the spell-checking for the quickbook files (possible, on demand, for not continuous using TextPad, my preferred text editor).  
  
(The only consolation is that most readers were undeterred, or not public-spirited enough to report them).   
  
Many thanks for this.

---

## Comment 7

> Username: NAThompson  
> Created_at: 2019-12-03 13:42:33 UTC  
> Url: https://github.com/boostorg/math/pull/275#issuecomment-561173116  

@jzmaddock : You good with merging or do you want to wait until you finish your other PRs to avoid merge conflicts?

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2019-12-03 17:22:32 UTC  
> Url: https://github.com/boostorg/math/pull/275#issuecomment-561270233  

I've just corrected one strange doubled-up work, but other than that all looks good so I've gone ahead and merged.  
  
This is really good useful stuff too - many thanks for this!!

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2019-12-03 17:23:17 UTC  
> Url: https://github.com/boostorg/math/pull/275#issuecomment-561270531  

Ooops, doubled up *word*.  You see, typos even here!

---

## Comment 10

> Username: bwignall  
> Created_at: 2019-12-05 21:40:11 UTC  
> Url: https://github.com/boostorg/math/pull/275#issuecomment-562330783  

@NAThompson , @pabristow : https://github.com/bwignall/typochecker now contains the script.

---

## Comment 11

> Username: pabristow  
> Created_at: 2019-12-06 09:21:42 UTC  
> Url: https://github.com/boostorg/math/pull/275#issuecomment-562497197  

Many thanks for this.  I'll give it a whirl on Boost.Multiprecision - though of course since I have carefully proof-read it recently there won't be any typos will there?   ;-)

---
