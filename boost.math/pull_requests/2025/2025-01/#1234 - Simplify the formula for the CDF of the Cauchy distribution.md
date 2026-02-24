# #1234 Simplify the formula for the CDF of the Cauchy distribution. [Merged]

> Username: WarrenWeckesser  
> Created at: 2025-01-17 02:22:41 UTC  
> Updated at: 2025-01-22 20:10:47 UTC  
> Merged at: 2025-01-17 13:12:47 UTC  
> Closed at: 2025-01-17 13:12:47 UTC  
> Url: https://github.com/boostorg/math/pull/1234  

The Cauchy CDF function may be expressed as atan2(1, -x)/pi.

---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2025-01-17 13:12:21 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/1234#pullrequestreview-2558981144  

This looks good to me. Thanks!. The only CI failure is an S390x runner failing to clone (which the CI manager is aware this has been occurring more frequently).

---

## Comment 2

> Username: WarrenWeckesser  
> Created_at: 2025-01-22 04:31:44 UTC  
> Url: https://github.com/boostorg/math/pull/1234#issuecomment-2606272015  

I should have also updated [`cauchy.qbk`](https://github.com/boostorg/math/blob/develop/doc/distributions/cauchy.qbk).  I'll create a new PR to update the CDF "Implementation Notes" section.  
  
Is there a guide or recipe somewhere for how to run quickbook to build the docs?

---

## Comment 3

> Username: mborland  
> Created_at: 2025-01-22 16:49:06 UTC  
> Url: https://github.com/boostorg/math/pull/1234#issuecomment-2607756169  

> I should have also updated [`cauchy.qbk`](https://github.com/boostorg/math/blob/develop/doc/distributions/cauchy.qbk). I'll create a new PR to update the CDF "Implementation Notes" section.  
>   
> Is there a guide or recipe somewhere for how to run quickbook to build the docs?  
  
There's a synopsis here: https://github.com/boostorg/math?tab=readme-ov-file#building-documentation. In practice you need some pretty old libraries and binaries to make it work so the CI will tell you if something is formatted incorrectly.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2025-01-22 17:51:54 UTC  
> Url: https://github.com/boostorg/math/pull/1234#issuecomment-2607894073  

>There's a synopsis here: https://github.com/boostorg/math?tab=readme-ov-file#building-documentation. In practice you need some pretty old libraries and binaries to make it work so the CI will tell you if something is formatted incorrectly.  
  
The markup is reasonably human readable - so what @mborland says, just commit, and we can always fix it up.  
  
BTW you shouldn't actually need old binaries though - the only thing that is version-fixed is the document DTD - though I confess I haven't updated anything in a good while ;)

---

## Comment 5

> Username: WarrenWeckesser  
> Created_at: 2025-01-22 19:48:29 UTC  
> Updated_at: 2025-01-22 19:58:15 UTC  
> Url: https://github.com/boostorg/math/pull/1234#issuecomment-2608128476  

> There's a synopsis here: ...  
  
Argh, right there in the README file!  Not sure how I missed that.  
  
On my Linux machine, I had to install xsltproc (I guess I had all the other dependencies already installed), and I changed `darwin` to `gcc` in `user-config.jam`.  
  
The HTML built successfully, and the change that I made looks OK.  For a change like this, do I also commit all the updated HTML files as part of the PR, or should I only submit the change to `cauchy.qbk`?

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2025-01-22 19:56:01 UTC  
> Url: https://github.com/boostorg/math/pull/1234#issuecomment-2608142352  

Just the .qbk file for the minute, we'll regenerate the docs prior to release (and the CI builds it too).  Thanks!

---

## Comment 7

> Username: WarrenWeckesser  
> Created_at: 2025-01-22 20:06:24 UTC  
> Updated_at: 2025-01-22 20:07:09 UTC  
> Url: https://github.com/boostorg/math/pull/1234#issuecomment-2608160909  

If the only change is in `cauchy.qbk`, should I used `[ci skip]` in the commit message?

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2025-01-22 20:10:46 UTC  
> Url: https://github.com/boostorg/math/pull/1234#issuecomment-2608170161  

No might as well let CI build as it checks the docs for validity.

---
