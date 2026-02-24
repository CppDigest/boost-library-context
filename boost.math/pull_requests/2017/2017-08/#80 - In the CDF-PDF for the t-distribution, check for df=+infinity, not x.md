# #80 In the CDF/PDF for the t-distribution, check for df=+infinity, not x [Closed]

> Username: sboukortt  
> Created at: 2017-08-18 09:36:01 UTC  
> Updated at: 2017-08-30 11:06:44 UTC  
> Closed at: 2017-08-29 16:01:40 UTC  
> Url: https://github.com/boostorg/math/pull/80  

A few elements make me suspect that `x` was a typo:  
- the comment says “+infinity”, but `x` could be negative; only `df` is necessarily positive;  
- `detail::check_x` a few lines earlier prevents `x` from being infinite anyway.  
  
However, I don’t think that it should affect the result, as the case where `df` is infinite is “caught” by `if (df > limit)`. In addition, if infinite values of `x` were allowed (it is not really clear to me why they are not?), then the return value would indeed be the same as for the normal distribution (0 or 1). The current code is merely suboptimal, not incorrect.

---

## Review 1 [Approved]

> Username: pabristow  
> Created_at: 2017-08-19 14:18:23 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/80#pullrequestreview-57351160  

You are right that there is some muddle here.   
  
I vacillated, oscillated even, between allowing infinity for parameters, or not.  It is much more complicated that I imagined when I started to allow infinity. It is fairly simple for plain built-ins like double, but some RealType, and some implementations do not have, or do not implement infinity, so you have to keep taking that into account.  And there were other difficulties with other distributions.  I've sure it is possible, but eventually I decided that there were other more useful, and more exciting things to do, and there still are, so it is in the pending drawer.  What is left is the cruft from previous partial permits of infinity.  
  
What is did also notice what a cut'n'paste error in the wrong function name in the error message "boost::math::pdf(const students_t_distribution<%1%>&, %1%)" (should be cdf) and that *is* worth changing.

---

## Comment 2

> Username: sboukortt  
> Created_at: 2017-08-19 18:18:46 UTC  
> Url: https://github.com/boostorg/math/pull/80#issuecomment-323539394  

Ah, I see. Thanks for the clarification.  
  
For the normal distribution, [support for infinity seems to be implemented like this](https://github.com/boostorg/math/blob/70e87cb407aba873498fe9db92c0c1ee24c04d32/include/boost/math/distributions/normal.hpp#L171). Is that code subject to the caveat that you mentioned, then?

---

## Comment 3

> Username: pabristow  
> Created_at: 2017-08-20 16:59:59 UTC  
> Url: https://github.com/boostorg/math/pull/80#issuecomment-323597682  

I believe that normal does support infinite values OK (if the platform and real type support infinity of course).  Please tell me if you find it doesn’t ☹  
  
(I lost the will to continue with all the many other distributions).  
  
Paul  
  
  
From: Sami Boukortt [mailto:notifications@github.com]  
Sent: 19 August 2017 19:19  
To: boostorg/math  
Cc: Paul A. Bristow; Comment  
Subject: Re: [boostorg/math] In the CDF/PDF for the t-distribution, check for df=+infinity, not x (#80)  
  
  
Ah, I see. Thanks for the clarification.  
  
For the normal distribution, support for infinity seems to be implemented like this<https://github.com/boostorg/math/blob/70e87cb407aba873498fe9db92c0c1ee24c04d32/include/boost/math/distributions/normal.hpp#L171>. Is that code subject to the caveat that you mentioned, then?  
  
—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/math/pull/80#issuecomment-323539394>, or mute the thread<https://github.com/notifications/unsubscribe-auth/ABBuAQ66tEhzvXyNpRvyfrGSOAgKj9l3ks5sZycHgaJpZM4O7Ulu>.

---

## Comment 4

> Username: sboukortt  
> Created_at: 2017-08-27 13:32:05 UTC  
> Url: https://github.com/boostorg/math/pull/80#issuecomment-325198593  

Hi again,  
Out of curiosity, does anything specific still need to be done before this can be merged?

---

## Comment 5

> Username: pabristow  
> Created_at: 2017-08-27 15:42:44 UTC  
> Url: https://github.com/boostorg/math/pull/80#issuecomment-325206228  

No – I think it will make its way into master and then into the next release 1.66  
  
Thanks for reporting this and providing a fix.  
  
From: Sami Boukortt [mailto:notifications@github.com]  
Sent: 27 August 2017 14:32  
To: boostorg/math  
Cc: Paul A. Bristow; Comment  
Subject: Re: [boostorg/math] In the CDF/PDF for the t-distribution, check for df=+infinity, not x (#80)  
  
  
Hi again,  
Out of curiosity, does anything specific still need to be done before this can be merged?  
  
—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/math/pull/80#issuecomment-325198593>, or mute the thread<https://github.com/notifications/unsubscribe-auth/ABBuAaKHgE3JlSnC6VPGvFbBweDVJO03ks5scW_WgaJpZM4O7Ulu>.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2017-08-28 17:11:42 UTC  
> Url: https://github.com/boostorg/math/pull/80#issuecomment-325415030  

Apologies for jumping in late here, you're correct that this needs fixing, but IMO the fix isn't quite right:  
  
* Later on, when df is greater than 1/eps the normal approximation is used anyway, so this check is entirely redundant since infinite degree of freedom is definitely > 1/eps.  
* We could deal with infinite x here if we wished, and simply return 0 (PDF or CDF at -INF) or 1 (CDF at +INF).

---

## Comment 7

> Username: pabristow  
> Created_at: 2017-08-28 17:19:02 UTC  
> Url: https://github.com/boostorg/math/pull/80#issuecomment-325417013  

OK – I’ll take a closer look at this.  
  
but I’m also concerned that the test matrix isn’t doing the tests.  
  
Paul  
  
  
  
From: jzmaddock [mailto:notifications@github.com]  
Sent: 28 August 2017 18:12  
To: boostorg/math  
Cc: Paul A. Bristow; Comment  
Subject: Re: [boostorg/math] In the CDF/PDF for the t-distribution, check for df=+infinity, not x (#80)  
  
  
Apologies for jumping in late here, you're correct that this needs fixing, but IMO the fix isn't quite right:  
  
  *   Later on, when df is greater than 1/eps the normal approximation is used anyway, so this check is entirely redundant since infinite degree of freedom is definitely > 1/eps.  
  *   We could deal with infinite x here if we wished, and simply return 0 (PDF or CDF at -INF) or 1 (CDF at +INF).  
  
—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/math/pull/80#issuecomment-325415030>, or mute the thread<https://github.com/notifications/unsubscribe-auth/ABBuATKMXTCH_rDw83YkHOTMYHw2jnlLks5scvTPgaJpZM4O7Ulu>.

---

## Comment 8

> Username: pabristow  
> Created_at: 2017-08-29 14:28:31 UTC  
> Url: https://github.com/boostorg/math/pull/80#issuecomment-325681796  

From: jzmaddock [mailto:notifications@github.com]  
Sent: 28 August 2017 18:12  
To: boostorg/math  
Cc: Paul A. Bristow; Comment  
Subject: Re: [boostorg/math] In the CDF/PDF for the t-distribution, check for df=+infinity, not x (#80)  
  
  
Apologies for jumping in late here, you're correct that this needs fixing, but IMO the fix isn't quite right:  
  
  *   Later on, when df is greater than 1/eps the normal approximation is used anyway, so this check is entirely redundant since infinite degree of freedom is definitely > 1/eps.  
  *   We could deal with infinite x here if we wished, and simply return 0 (PDF or CDF at -INF) or 1 (CDF at +INF).  
Added these special cases and some tests,  
and made changes to allow infinite DF.  
Now I remember why I decided not to stir this hornets’ nest ;-)  
This has ramifications in lots of places.  
I’ve had to add to common_handling and an extra template parameter to test_out_of_range to allow infinity tests.  
Would be neat to have an analog of boost::math::tools::max_value that includes infinity – perhaps called  
boost::math::tools::infinite_value  
that I’ve approximated with  
(::std::numeric_limits<RealType>::is_specialized & ::std::numeric_limits<RealType>::has_infinity) ? +std::numeric_limits<RealType>::infinity() : boost::math::tools::max_value<RealType>())  
and also perhaps boost::math::tools::infinite_negative_value?  
I also note that this appears to be gratuitous  causing lots of clutter?  
1>test_students_t.cpp  
1>I:\modular-boost\boost/math/special_functions/next.hpp(32): warning C4099: 'boost::math::concepts::real_concept': type name first seen using 'class' now seen using 'struct'  
1>I:\modular-boost\boost/math/concepts/real_concept.hpp(60): note: see declaration of 'boost::math::concepts::real_concept'  
   namespace concepts {  
      struct real_concept;  
      struct std_real_concept;  
  }  
Can these structs be named class instead - or am I missing something like public/private-ness?  
To git@github.com:boostorg/math.git  
   37c71f7..34fd59c  develop -> develop  
tested on VC 141 and GCC 7.1.0 but will check on how the other test platforms get on.  
TODO update docs if all this works as planned.  
Hope this is better.  
Paul

---

## Comment 9

> Username: pabristow  
> Created_at: 2017-08-30 11:06:44 UTC  
> Url: https://github.com/boostorg/math/pull/80#issuecomment-325958323  

I've made a fuler revision of this allowing infinite argument and random variate, and updated docs to say so.  
   1527667..9921c27  develop -> develop

---
