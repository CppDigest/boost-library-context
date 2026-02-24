# #25 Gcd unit tests [Merged]

> Username: jeremy-murphy  
> Created at: 2016-03-02 12:07:10 UTC  
> Updated at: 2016-03-09 18:51:25 UTC  
> Merged at: 2016-03-09 18:51:25 UTC  
> Closed at: 2016-03-09 18:51:25 UTC  
> Url: https://github.com/boostorg/math/pull/25  

A few basic gcd unit tests.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2016-03-09 01:24:18 UTC  
> Url: https://github.com/boostorg/math/pull/25#issuecomment-194054986  

Any thoughts on this PR? I think it is fine as is although minimal; I could add some more variety to the values tested just to increase confidence in correctness.

---

## Comment 2

> Username: pabristow  
> Created_at: 2016-03-09 09:35:15 UTC  
> Url: https://github.com/boostorg/math/pull/25#issuecomment-194208419  

You have never have too many tests ;-)  
  
(and examples of use).  
  
Paul  
  
From: Jeremy W. Murphy [mailto:notifications@github.com]   
Sent: 09 March 2016 01:24  
To: boostorg/math  
Subject: Re: [math] Gcd unit tests (#25)  
  
Any thoughts on this PR? I think it is fine as is although minimal; I could add some more variety to the values tested just to increase confidence in correctness.  
  
—  
Reply to this email directly or view it on GitHub https://github.com/boostorg/math/pull/25#issuecomment-194054986 .  https://github.com/notifications/beacon/ABBuAVQ2jgbrIcxTmRoxQmxH2GrJQdgSks5priFCgaJpZM4HnYBR.gif

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2016-03-09 11:01:31 UTC  
> Url: https://github.com/boostorg/math/pull/25#issuecomment-194241904  

Well, I think that's mostly true because most of the time people don't write enough tests.  
  
But in the case of algorithms as simple as Euclidean gcd, I struggle to think of new examples that actually test something novel. Admittedly, 'binary' gcd is more complex, but there just aren't many code paths through Euclidean gcd, right? (And I intend to make it even simpler.)  
  
I really value good tests, but I don't want to add more just for the sake of it (slightly contrary to what I said above). I'll keep thinking about it for a few days and I welcome suggestions.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2016-03-09 13:40:43 UTC  
> Url: https://github.com/boostorg/math/pull/25#issuecomment-194300365  

Looks good to me, have you checked code coverage?

---
