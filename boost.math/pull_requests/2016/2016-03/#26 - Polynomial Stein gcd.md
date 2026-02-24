# #26 Polynomial Stein gcd [Closed]

> Username: jeremy-murphy  
> Created at: 2016-03-02 12:20:33 UTC  
> Updated at: 2016-03-25 11:29:23 UTC  
> Closed at: 2016-03-25 11:29:23 UTC  
> Url: https://github.com/boostorg/math/pull/26  

This is the basic implementation required for using the "binary" gcd algorithm on a polynomial.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2016-03-09 01:29:45 UTC  
> Url: https://github.com/boostorg/math/pull/26#issuecomment-194056094  

Any thoughts here? I realize using `operator <` this way might be controversial.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2016-03-09 18:57:26 UTC  
> Url: https://github.com/boostorg/math/pull/26#issuecomment-194451613  

If we're going to have an operator <, then if the limb counts are the same, we should compare limb-by-limb I guess, with higher order limbs taking priority.  
  
Is there precedent for polynomial ordering?

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2016-03-10 01:55:13 UTC  
> Url: https://github.com/boostorg/math/pull/26#issuecomment-194616574  

Unfortunately, 99% of web results for polynomial ordering are about ordering the terms within a single expression and polynomial sorting is, well, about polynomial algorithms for sorting.  :)  I looked into this topic last year and my conclusion was that there was no precedent.  
  
I believe `<` by itself is OK, it satisfies the concept of Strict Weak Ordering, my main concern is the interaction with `==`: elements that are equivalent according to the `<` relation are not necessarily equal according to `==`. Whereas they would be if `<` was a Total Ordering (I think -- I still get the strict/weak/partial/total terminology a bit mixed up). But is that terrible? My understanding is that the STL components only require a Strict Weak Ordering.  
  
An argument in favour of this definition of `<` is that it is the most efficient for Stein gcd: a total ordering would also work but increases the complexity of ranking from constant to linear.

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2016-03-10 01:58:32 UTC  
> Url: https://github.com/boostorg/math/pull/26#issuecomment-194617378  

Gah, I accidentally called the branch "Steiner" when I meant "Stein", how embarrassing.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2016-03-10 09:03:34 UTC  
> Url: https://github.com/boostorg/math/pull/26#issuecomment-194744290  

On 10/03/2016 01:58, Jeremy W. Murphy wrote:  
  
> Gah, I accidentally called the branch "Steiner" when I meant "Stein",   
> how embarrassing.  
  
I suspect he'll live with it ;)

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2016-03-10 09:05:30 UTC  
> Url: https://github.com/boostorg/math/pull/26#issuecomment-194744698  

On 10/03/2016 01:55, Jeremy W. Murphy wrote:  
  
> Unfortunately, 99% of web results for polynomial ordering are about   
> ordering the terms within a single expression and polynomial sorting   
> is, well, about polynomial algorithms for sorting. :) I looked into   
> this topic last year and my conclusion was that there was no precedent.  
>   
> I believe |<| by itself is OK, it satisfies the concept of Strict Weak   
> Ordering, my main concern is the interaction with |==|: elements that   
> are equivalent according to the |<| relation are not necessarily equal   
> according to |==|. Whereas they would be if |<| was a Total Ordering   
> (I think -- I still get the strict/weak/partial/total terminology a   
> bit mixed up). But is that terrible? My understanding is that the STL   
> components only require a Strict Weak Ordering.  
>   
> An argument in favour of this definition of |<| is that it is the most   
> efficient for Stein gcd: a total ordering would also work but   
> increases the complexity of ranking from constant to linear.  
  
Understood, I'm just uncomfortable with this definition as is.  
  
Can we either pass a functor to gcd for operator <, or else use a traits   
class for that operation within gcd, and then we could leave operator<   
undefined for polynomials?

---

## Comment 7

> Username: pabristow  
> Created_at: 2016-03-10 10:12:46 UTC  
> Url: https://github.com/boostorg/math/pull/26#issuecomment-194773061  

I trust you guys are saving this erudite discussion to record in the Boost.Math documentation ;-)  
  
But I’ll do nothing until it has come to a conclusion…  
  
Paul  
  
From: jzmaddock [mailto:notifications@github.com]   
Sent: 10 March 2016 09:06  
To: boostorg/math  
Subject: Re: [math] Polynomial Stein gcd (#26)  
  
On 10/03/2016 01:55, Jeremy W. Murphy wrote:  
  
> Unfortunately, 99% of web results for polynomial ordering are about   
> ordering the terms within a single expression and polynomial sorting   
> is, well, about polynomial algorithms for sorting. :) I looked into   
> this topic last year and my conclusion was that there was no precedent.  
>   
> I believe |<| by itself is OK, it satisfies the concept of Strict Weak   
> Ordering, my main concern is the interaction with |==|: elements that   
> are equivalent according to the |<| relation are not necessarily equal   
> according to |==|. Whereas they would be if |<| was a Total Ordering   
> (I think -- I still get the strict/weak/partial/total terminology a   
> bit mixed up). But is that terrible? My understanding is that the STL   
> components only require a Strict Weak Ordering.  
>   
> An argument in favour of this definition of |<| is that it is the most   
> efficient for Stein gcd: a total ordering would also work but   
> increases the complexity of ranking from constant to linear.  
  
Understood, I'm just uncomfortable with this definition as is.  
  
Can we either pass a functor to gcd for operator <, or else use a traits   
class for that operation within gcd, and then we could leave operator<   
undefined for polynomials?  
  
—  
Reply to this email directly or view it on GitHub https://github.com/boostorg/math/pull/26#issuecomment-194744698 .  https://github.com/notifications/beacon/ABBuAbfMLRZYpgv1LgNuUsJLEpsc-gayks5pr97agaJpZM4HnYlS.gif

---

## Comment 8

> Username: jeremy-murphy  
> Created_at: 2016-03-14 07:35:17 UTC  
> Url: https://github.com/boostorg/math/pull/26#issuecomment-196183547  

I think a traits class sounds like a good idea. It may well be too much to expect that a single `operator<` will optimally perform all the tasks expected of it for a polynomial. In the end, given that equality is defined by `a.data() == b.data()`, it is probably consistent for less than to be `a.data() < b.data()`.  
  
What about right shift? I guess we could have a complementary left shift that increases the order of all the terms and adds a zero constant to the end. That would make the behaviour equivalent to integers, I think.

---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2016-03-25 11:29:23 UTC  
> Url: https://github.com/boostorg/math/pull/26#issuecomment-201245442  

I'll close this PR since I think the final result will look somewhat different.

---
