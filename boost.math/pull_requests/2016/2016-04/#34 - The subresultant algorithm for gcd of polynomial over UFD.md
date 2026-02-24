# #34 The subresultant algorithm for gcd of polynomial over UFD [Merged]

> Username: jeremy-murphy  
> Created at: 2016-04-20 13:12:58 UTC  
> Updated at: 2017-04-20 18:40:51 UTC  
> Merged at: 2017-04-20 18:40:51 UTC  
> Closed at: 2017-04-20 18:40:51 UTC  
> Url: https://github.com/boostorg/math/pull/34  

_This is done! Please review in detail._  
  
**Algorithm 4.6.1C: Greatest common divisor over a unique factorization domain.**  
  
The subresultant algorithm from Knuth is a missing piece of the polynomial gcd puzzle, but does not close the gap entirely.   
  
As I mentioned in conversation elsewhere on this PR, the algorithm needs to calculate intermediate values of size exponentially larger than the inputs, which rapidly leads to overflow of the intermediate variables for even trivially small input values. This turns out to be a common problem in integer algorithms and is called _intermediate expression swell_. It's quite likely that better algorithms than this one will not be entirely free of multi-precision integers, so it should not (yet) be taken as an intrinsic weakness of this algorithm.

---

## Comment 1

> Username: pabristow  
> Created_at: 2016-04-20 14:06:25 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-212440005  

I feel there must be some documentation changes from all this work ;-)  
  
Tell me if you want me to do anything.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2016-04-20 17:15:45 UTC  
> Updated_at: 2016-08-25 13:19:23 UTC  
> Url: https://github.com/boostorg/math/pull/34#discussion_r60449716  

Is it possible to write this without a goto?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2016-04-20 17:16:03 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-212519961  

Paul, did you mean to close this?

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2016-04-20 21:46:45 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-212621822  

For this specific PR yes we can remove the statement that gcd only works  
for polynomials over a field but don't worry I can do that. :)  
On 21 Apr 2016 12:06 AM, "Paul A. Bristow" notifications@github.com wrote:  
  
> I feel there must be some documentation changes from all this work ;-)  
>   
> Tell me if you want me to do anything.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/math/pull/34#issuecomment-212440005

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2016-04-20 21:54:26 UTC  
> Updated_at: 2016-08-25 13:19:23 UTC  
> Url: https://github.com/boostorg/math/pull/34#discussion_r60494660  

Yes although I left the goto in because I wasn't immediately sure how to  
take it out without double-checking the termination condition. And I find  
anything taboo strangely fascinating. :)  
On 21 Apr 2016 3:15 AM, "jzmaddock" notifications@github.com wrote:  
  
> In include/boost/math/tools/polynomial.hpp  
> https://github.com/boostorg/math/pull/34#discussion_r60449716:  
>   
> > +  
> > -    polynomial<U> u(a), v(b);  
> > -    U const d = detail::reduce_to_primitive(u, v);  
> > -    U g = 1, h = 1;  
> > -    polynomial<U> r;  
> > -    while (true)  
> > -    {  
> > -        // Pseudo-division.  
> > -        N const delta = u.degree() - v.degree();  
> > -        r = u % v;  
> > -        if (!r)  
> > -            goto gcd_end;  
> > -        if (r.degree() == 0)  
> > -        {  
> > -            v = polynomial<U>(U(1));  
> > -            goto gcd_end;  
>   
> Is it possible to write this without a goto?  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/math/pull/34/files/d484fa657c65cc8a233e1aeabb72c2e7199c581e#r60449716

---

## Comment 6

> Username: pabristow  
> Created_at: 2016-04-21 09:17:12 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-212825069  

Oops- no of course not - sorry – speed of mouse deceived the brain.  
  
From: jzmaddock [mailto:notifications@github.com]   
Sent: 20 April 2016 18:16  
To: boostorg/math  
Cc: Paul A. Bristow; State change  
Subject: Re: [boostorg/math] gcd of polynomial over unique factorization domain (#34)  
  
Paul, did you mean to close this?  
  
—  
You are receiving this because you modified the open/close state.  
Reply to this email directly or view it on GitHub https://github.com/boostorg/math/pull/34#issuecomment-212519961   https://github.com/notifications/beacon/ABBuAVTw8sidCZq1pQV0um3Ul2WrsrtZks5p5l9UgaJpZM4ILrWG.gif

---

## Comment 7

> Username: jeremy-murphy  
> Created_at: 2016-04-21 11:03:54 UTC  
> Updated_at: 2016-08-25 13:19:23 UTC  
> Url: https://github.com/boostorg/math/pull/34#discussion_r60562383  

Well, it's easy enough to just replace each goto with the return statement  
but then you end up with a duplicate expression. I'll do that for now  
because it's trivial and probably a better place to progress from.  
On 21 Apr 2016 7:54 AM, "Jeremy Murphy" jeremy.william.murphy@gmail.com  
wrote:  
  
> Yes although I left the goto in because I wasn't immediately sure how to  
> take it out without double-checking the termination condition. And I find  
> anything taboo strangely fascinating. :)  
> On 21 Apr 2016 3:15 AM, "jzmaddock" notifications@github.com wrote:  
>   
> > In include/boost/math/tools/polynomial.hpp  
> > https://github.com/boostorg/math/pull/34#discussion_r60449716:  
> >   
> > > +  
> > > -    polynomial<U> u(a), v(b);  
> > > -    U const d = detail::reduce_to_primitive(u, v);  
> > > -    U g = 1, h = 1;  
> > > -    polynomial<U> r;  
> > > -    while (true)  
> > > -    {  
> > > -        // Pseudo-division.  
> > > -        N const delta = u.degree() - v.degree();  
> > > -        r = u % v;  
> > > -        if (!r)  
> > > -            goto gcd_end;  
> > > -        if (r.degree() == 0)  
> > > -        {  
> > > -            v = polynomial<U>(U(1));  
> > > -            goto gcd_end;  
> >   
> > Is it possible to write this without a goto?  
> >   
> > —  
> > You are receiving this because you authored the thread.  
> > Reply to this email directly or view it on GitHub  
> > https://github.com/boostorg/math/pull/34/files/d484fa657c65cc8a233e1aeabb72c2e7199c581e#r60449716

---

## Comment 8

> Username: jeremy-murphy  
> Created_at: 2016-04-28 08:28:03 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-215350928  

I'm not sure how to reopen -- is it only the closer that can do that?

---

## Comment 9

> Username: pabristow  
> Created_at: 2016-04-28 08:39:56 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-215353564  

Sorry about my closing mistake.   Hope this does the trick.

---

## Comment 10

> Username: jeremy-murphy  
> Created_at: 2016-04-28 08:40:42 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-215353748  

Thanks, Paul.

---

## Comment 11

> Username: jeremy-murphy  
> Created_at: 2016-05-01 11:56:04 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-216037066  

The pseudo-division implicit in this algorithm results in intermediate values of enormous magnitude; I currently can't imagine anyone using it safely except on a arbitrary precision type.  
For example, calculating the gcd of two polynomials of degree 7 with coefficients in the range 0-31 requires intermediate values that require at least 64-bits of storage.  
So I'm inclined to restrict this to non-POD coefficient types for the time being, although that's hardly foolproof. Thoughts?

---

## Comment 12

> Username: jeremy-murphy  
> Created_at: 2016-05-01 13:13:37 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-216041501  

Actually, let me update that now that I got the multiprecision to work, it sometimes requires 157 bits of storage.

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2016-05-01 16:44:06 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-216054569  

On 01/05/2016 14:13, Jeremy W. Murphy wrote:  
  
> Actually, let me update that now that I got the multiprecision to   
> work, it sometimes requires 157 bits of storage.  
  
Ouch!  Is this division in general of gcd in particular?  
  
Can we use binary gcd and avoid division completely in that case?  
  
John.

---

## Comment 14

> Username: jeremy-murphy  
> Created_at: 2016-05-02 04:10:52 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-216102198  

It's the pseudo-division inherent in quotient and remainder of polynomials  
over a ufd, so it's not an issue with gcd per se.  
But yeah, maybe we're just restricted to using Stein gcd on polynomials  
over a ufd.  
However, last time I tested that, the results were not great...  
  
On 2 May 2016 at 02:44, jzmaddock notifications@github.com wrote:  
  
> On 01/05/2016 14:13, Jeremy W. Murphy wrote:  
>   
> > Actually, let me update that now that I got the multiprecision to  
> > work, it sometimes requires 157 bits of storage.  
>   
> Ouch! Is this division in general of gcd in particular?  
>   
> Can we use binary gcd and avoid division completely in that case?  
>   
> John.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/math/pull/34#issuecomment-216054569

---

## Comment 15

> Username: jeremy-murphy  
> Created_at: 2016-06-18 12:33:38 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-226939380  

I finally got my hands on a book of computer algebra, and it turns out that there are better, more tailored algorithms for gcd of polynomials over a ufd, such as applying modular homomorphisms to reduce the problem to gcd of polynomials over a finite field, as actually mentioned on the [Wikipedia page](https://en.wikipedia.org/wiki/Polynomial_greatest_common_divisor#Modular_GCD_algorithm).  
  
Now, I assume that although the aforementioned algorithm will become the default for single-precision coefficients, this algorithm might still have a role to play, as presumably there will be some polynomials, dense or sparse, with multi-precision coefficients, that are more easily calculated by this algorithm than the other. (Apparently the modular gcd is inefficient with sparse polynomials.)  
  
So I suggest we keep it, but I think it should be limited to only work on multi-precision coefficients. Of course, curious and reckless users can always find a way to use it for single-precision coefficients, but I just think it would be negligent for us to let them do that _with ease_. What do you think?

---

## Comment 16

> Username: jeremy-murphy  
> Created_at: 2016-06-18 12:33:51 UTC  
> Updated_at: 2016-06-18 12:34:40 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-226939401  

Whoops, it really is easy to click the wrong button.  :)

---

## Comment 17

> Username: jeremy-murphy  
> Created_at: 2016-06-19 12:03:19 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-226993746  

Aha, I just read more of section 4.6.2 (factorization of polynomials) in which Knuth discusses but does not quite define the aforementioned modular gcd algorithm. He also says  
  
> Perhaps the best general procedure would be to start with the computation of gcd(_u_(_x_), _v_(_x_)) modulo a fairly small prime _p_, not a divisor of both _l_(_u_) and _l_(_v_). If the result _q_(_x_) is 1, we're done; if it has high degree, we use Algorithm 4.6.1C; otherwise we use one of the methods above...  
  
Meaning, one of the modular methods.

---

## Comment 18

> Username: jzmaddock  
> Created_at: 2016-06-19 12:13:21 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-226994127  

On 18/06/2016 13:33, Jeremy W. Murphy wrote:  
  
> I finally got my hands on a book of computer algebra, and it turns out   
> that there are better, more tailored algorithms for gcd of polynomials   
> over a ufd, such as applying modular homomorphisms to reduce the   
> problem to gcd of polynomials over a finite field, as actually   
> mentioned on the Wikipedia page   
> https://en.wikipedia.org/wiki/Polynomial_greatest_common_divisor#Modular_GCD_algorithm.  
>   
> Now, I assume that although the aforementioned algorithm will become   
> the default for single-precision coefficients, this algorithm might   
> still have a role to play, as presumably there will be some   
> polynomials, dense or sparse, with multi-precision coefficients, that   
> are more easily calculated by this algorithm than the other.   
> (Apparently the modular gcd is inefficient with sparse polynomials.)  
>   
> So I suggest we keep it, but I think it should be limited to only work   
> on multi-precision coefficients. Of course, curious and reckless users   
> can always find a way to use it for single-precision coefficients, but   
> I just think it would be negligent for us to let them do that /with   
> ease/. What do you think?  
  
I confess to know next to nothing about number theory.  Empirical   
scientist type here!  
  
 From what I've read, the modular version is of greatest importance for   
say rational types where even just a few intermediate calculations can   
cause all kinds issues.  For all other types it probably comes down to a   
bit of empirical testing to determine which method is best for which   
number type, and then use some kind of traits class / heuristic to   
dispatch to the best (or approximately the best) algorithm?

---

## Comment 19

> Username: jeremy-murphy  
> Created_at: 2016-06-19 12:24:52 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-226994621  

Btw, I'm also thinking to move the gcd functions into a separate file `polynomial_gcd.hpp` in the `tools` directory, not for any especially logical reason, it just seems neater. Is that OK or do you prefer it all remains in the one file?

---

## Comment 20

> Username: jzmaddock  
> Created_at: 2016-06-19 16:13:06 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-227005608  

On 19/06/2016 13:24, Jeremy W. Murphy wrote:  
  
> Btw, I'm also thinking to move the gcd functions into a separate file   
> |polynomial_gcd.hpp| in the |tools| directory, not for any especially   
> logical reason, it just seems neater. Is that OK or do you prefer it   
> all remains in the one file?  
  
Seems logical/fine to me.

---

## Comment 21

> Username: jeremy-murphy  
> Created_at: 2016-07-15 08:24:05 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-232892251  

OK, the algorithm and unit tests are done, so please review them in detail at your leisure.  
  
I introduced `leading_coefficient` for convenience with gcd algorithms, but maybe it should go in the base polynomial header file?  
  
It's currently in the `boost::math::tools` namespace and, without really thinking about it, I also put an overload of `gcd` for integer polynomials in that namespace too. I think I'll take the `gcd` overload out though, because as I mentioned earlier, I don't think anyone wants to get surprised by this algorithm.

---

## Comment 22

> Username: jeremy-murphy  
> Created_at: 2016-07-15 08:25:54 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-232892578  

Oh and documentation of this algorithm should include some warning about intermediate value swell making it generally unsuitable for anything but arbitrary precision coefficients or trivially small inputs.

---

## Comment 23

> Username: jeremy-murphy  
> Created_at: 2016-08-25 13:21:57 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-242383480  

Hey, I'm still alive, and I would still like to finish this PR. Is there anything you think is missing? I have put a paragraph about the algorithm in the docs but there are no examples of usage for gcd on polynomials yet.

---

## Comment 24

> Username: jzmaddock  
> Created_at: 2016-08-26 17:48:25 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-242803690  

Well I'm certainly glad you're still alive ;)  My apologies I _have_ been slow to get to this.... right now I'd like to keep the develop branch clean in case we have any late SNAFU's that need fixing for the upcoming release.... so I've pulled your changes to branch "gcd_polynomial" and made a few small changes:  
- Code now compiles with msvc (not really sure why it didn't to be honest).  
- The new gcd in boost::math::tools is pulled into boost::math via a using declaration - that way we both overload boost::math::gcd and provide a version in the same namespace as polynomial for ADL lookup.  
- polynomial.hpp includes the new gcd code by default, so there's no need for a separate header include - my feeling is that code behaviour shouldn't change depending which headers you include.  
- I've updated the new gcd overload's disable_if conditions to reject things which aren't integers (to match the function it calls).  
  
Does this look OK to you?  
  
Over than that it looks fine, but more comments to follow shortly...

---

## Comment 25

> Username: jzmaddock  
> Created_at: 2016-08-26 18:17:09 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-242811245  

OK, back from real-life duties... my remaining comments are more like open questions:  
  
What should happen if we call gcd(x, y) on each of the following types:  
- Built-in integers?  Currently fails deep inside the default gcd code.  We could probably do better with another overload and a static_assert explaining what steps to take.  
- Fixed precision UDT's?  For example boost::multiprecision::int256_t.  We could restrict the new code to unbounded integers if that was best.  
- Unsigned integers?  
- Floating point types (including extended precision UDT's)?  Currently compiles, but is this expected to work?  I assume so as it goes to the Euclid algorithm.  
- Rational types?  Currently goes to the Euclid algorithm, but presumably suffers from the same issues as regular integer types for fixed precision rationals?  
  
Thanks!

---

## Comment 26

> Username: jeremy-murphy  
> Created_at: 2016-08-27 10:08:43 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-242908545  

Hi John. I haven't looked at the changes yet but what you describe in your first message all sounds fine.  
The only part I'm curious to understand better is the separation of functionality into different header files. I've noticed that some Boost libraries will put some non-member class functions in separate files, and I'm not entirely sure of the rationale. The best example is probably iostream insertion and extraction operators, which I understand is to avoid including the heavy <iostream>. But then for example Boost.Units separates the arithmetic operators from the quantity type, which seems weird because they're required for basic usage. Is that just about minute control of what is in out of the namespace at any given time?

---

## Comment 27

> Username: jeremy-murphy  
> Created_at: 2016-08-27 10:09:46 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-242908600  

I'll comment on the second list of items soon.

---

## Comment 28

> Username: jzmaddock  
> Created_at: 2016-08-27 11:51:47 UTC  
> Url: https://github.com/boostorg/math/pull/34#issuecomment-242912838  

On 27/08/2016 11:08, Jeremy W. Murphy wrote:  
  
> Hi John. I haven't looked at the changes yet but what you describe in   
> your first message all sounds fine.  
> The only part I'm curious to understand better is the separation of   
> functionality into different header files. I've noticed that some   
> Boost libraries will put some non-member class functions in separate   
> files, and I'm not entirely sure of the rationale. The best example is   
> probably iostream insertion and extraction operators, which I   
> understand is to avoid including the heavy . But then for example   
> Boost.Units separates the arithmetic operators from the quantity type,   
> which seems weird because they're required for basic usage. Is that   
> just about minute control of what is in out of the namespace at any   
> given time?  
  
Generally speaking we do tend to go for reasonably fine grained control   
and "only pay for what you need" - when it's appropriate to do so - in   
this case if we're overloading another function then my gut says it   
should always be overloaded, and not just if you include some other   
header.  ie no change in behaviour based on which headers you include   
and in which order.  Besides the additional header isn't that heavyweight?

---
