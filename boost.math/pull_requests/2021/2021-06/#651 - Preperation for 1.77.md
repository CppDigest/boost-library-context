# #651 Preperation for 1.77: [Merged]

> Username: jzmaddock  
> Created at: 2021-06-27 18:39:05 UTC  
> Updated at: 2021-06-29 15:53:00 UTC  
> Merged at: 2021-06-29 09:45:55 UTC  
> Closed at: 2021-06-29 09:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/651  

Rework hypergeometric distro equations.  
Fix up Fibonacci docs.  
Fix Fibonacci constexpr and noexcept usage and add to testing.  
Regenerate docs.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-06-27 18:40:50 UTC  
> Url: https://github.com/boostorg/math/pull/651#issuecomment-869207748  

@pabristow : this includes reworked hypergeometric distribution equations, can I get you to double check that I haven't fat-fingered them again?  See https://github.com/boostorg/math/blob/d4de174bbbd53f6e13e6d078312f5adfd0683310/doc/equations/hypergeometric6.svg

---

## Comment 2

> Username: pabristow  
> Created_at: 2021-06-28 10:09:40 UTC  
> Url: https://github.com/boostorg/math/pull/651#issuecomment-869556546  

@jzmaddock Sadly, your Greek is rusty - kurtosis has a 'u' not a 'e' ?  https://en.wikipedia.org/wiki/Kurtosis   
  
Why they couldn't Pearson have called it tailedness? Obfuscation.  
  
More seriously, I can't reconcile this equation with Wolfram   
  
https://www.wolframalpha.com/input/?i=kurtosis+hypergeometric+distribution  
  
used in my PR  (I'd trust this one as it is computer generated.  
  
or Wikipedia https://en.wikipedia.org/wiki/Hypergeometric_distribution  
  
even allowing for the confusion caused by using different letter symbols for the variables :-(  
  
Should I also implement this formula in C++ to confirm that it is also correct?

---

## Comment 3

> Username: pabristow  
> Created_at: 2021-06-28 11:22:06 UTC  
> Url: https://github.com/boostorg/math/pull/651#issuecomment-869601877  

Also can we be explicit that this is the kurtosis and not the excess kurtosis?

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-06-28 11:59:18 UTC  
> Url: https://github.com/boostorg/math/pull/651#issuecomment-869622799  

>@jzmaddock Sadly, your Greek is rusty - kurtosis has a 'u' not a 'e' ? https://en.wikipedia.org/wiki/Kurtosis  
  
:(  
  
Well at least that one is an easy fix!!  
  
>More seriously, I can't reconcile this equation with Wolfram  
>  
>https://www.wolframalpha.com/input/?i=kurtosis+hypergeometric+distribution  
>  
>used in my PR (I'd trust this one as it is computer generated.  
  
I had trouble getting my head round that one so I used Wikipedia...  
  
Maybe I'll look at wolfram again.  
  
>or Wikipedia https://en.wikipedia.org/wiki/Hypergeometric_distribution  
  
It should be identical to the Wikipedia definition - that's where I copied it from, it is also marked as Kurtosis Excess on the wikipedia page  
  
>even allowing for the confusion caused by using different letter symbols for the variables :-(  
  
Yeah, that sucks, it does help though that the formula are all symmetrical in n and r (or whatever you call them).

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2021-06-28 12:01:05 UTC  
> Url: https://github.com/boostorg/math/pull/651#issuecomment-869623828  

Bollocks, the wolfram version isn't symmetrical in n and r :(

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2021-06-28 12:42:54 UTC  
> Url: https://github.com/boostorg/math/pull/651#issuecomment-869651590  

Ah, kurtosis excess is: https://www.wolframalpha.com/input/?i=kurtosis+excess+hypergeometric+distribution

---

## Comment 7

> Username: pabristow  
> Created_at: 2021-06-28 15:51:06 UTC  
> Url: https://github.com/boostorg/math/pull/651#issuecomment-869799859  

I did check that the Wolfram and wikipedia equations when translated into C++ give the same numerical result.  
  
It feels odd to me that the number of selections should be 'allowed' to be more than the number of trials, as implied by symmetry in n and r?   
  
I started to add a Boost check that failures/successes/events <= trials, but then I discovered the symmetry.   
  
Or am I terminally confused as usual?  
  
Since we (I) have used Wolfram, can't we just refer to the Wolfram alpha page, and duck the issue?

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2021-06-28 17:18:57 UTC  
> Url: https://github.com/boostorg/math/pull/651#issuecomment-869867128  

I think it's quite nice to have the equation inline in that it makes the text easier to read.  
  
How about this, basically cut and pasted from the wolfram page:   
![preview](https://user-images.githubusercontent.com/5011768/123677639-48beca80-d83d-11eb-9542-671e52183bc6.png)

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2021-06-28 17:39:17 UTC  
> Url: https://github.com/boostorg/math/pull/651#issuecomment-869881296  

>It feels odd to me that the number of selections should be 'allowed' to be more than the number of trials, as implied by symmetry in n and r?  
  
If there are r defectives in a population of size N and we sample n of them, then necessarily we find some number k defectives with:  
  
k <= min(r, n)  
r <= N  
n <= N  
  
Notice the symmetry again, and no relation between n and r.  
  
In fact the distribution is *necessarily* symmetrical in n and r if you look at it this way:  we have a population of size N, and two independent events happening to it: the number of failures within it, and the number of times it gets sampled.  View the distribution as the union between these two events, and remember that the names we've given them "failures" and "samples" are quite arbitrary, and the symmetry becomes obvious.  
  
But I confess not to have thought about it before! ;)

---

## Comment 10

> Username: pabristow  
> Created_at: 2021-06-29 11:38:36 UTC  
> Url: https://github.com/boostorg/math/pull/651#issuecomment-870519374  

This at last makes sense to me.    
" View the distribution as the union between these two events,"  
is the key (except that using the word 'events' might confuse some readers of definitions using 'events' only for the failures/success).    
  
Thanks.  
  
PS Another possibly interesting question is how can the kurtosis be useful?  But that's probably Off-Topic :-)  
  
(Only very few events from a large population shifts the kurtosis from above about 3 - but that's obviously raising the outlier risk.)

---
