# #147 Kummer-M [Closed]

> Username: NAThompson  
> Created at: 2018-09-03 05:11:49 UTC  
> Updated at: 2018-09-04 18:32:41 UTC  
> Closed at: 2018-09-03 23:40:23 UTC  
> Url: https://github.com/boostorg/math/pull/147  

This is my first special function commit, and frankly, it's not an area of mathematics in which I have a lot of expertise, so I'm hoping gauge if anyone is interested in going forward with this to help me out. I think using the confluent hypergeometric function will be much easier than the 2F1,  so it might be a nice starting point to move into the "higher analysis" of generalized hypergeometrics like Appell series.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-09-03 07:24:31 UTC  
> Url: https://github.com/boostorg/math/pull/147#issuecomment-418024677  

Great!  Have you seen: https://github.com/boostorg/math/tree/hypergeometric_soc_2014 ?  
  
This has the almost trivial 0F1, 1F0 and 2F0 done and dusted, I actually got stuck on 1F1 because there is a range of parameters I simply couldn't get working.  I suspect the "answer" is to give up and throw an evaluation error in that area.  Anyhow there is some good test data for 1F1 there which highlights the problem areas.  Can you compare the 2 versions and see where we're at?  
  
BTW the code in this branch originates from https://github.com/BoostGSoC14/boost.math which has a good selection of nicely written implementation methods, but ran out of time before rigorous testing and getting the method selection logic sorted out.  Unfortunately that seems to be the time consuming bit :(

---

## Comment 2

> Username: NAThompson  
> Created_at: 2018-09-03 10:36:37 UTC  
> Url: https://github.com/boostorg/math/pull/147#issuecomment-418074839  

Have you seen the asymptotics from Handbook of Exact Solutions to Ordinary Differential Equations, S.2.7-6, by Andrei Polyanin? I think these may expressions may be of use for the large parameters you speak of, and were going to be my next line of attack.  
  
It looks like your implementation already has a lot of good stuff, so should I just close this PR and start working on your branch or do you see anything of value here (the doc writeup might be used)?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2018-09-03 17:16:38 UTC  
> Url: https://github.com/boostorg/math/pull/147#issuecomment-418164429  

>Have you seen the asymptotics from Handbook of Exact Solutions to Ordinary Differential Equations, S.2.7-6, by Andrei Polyanin? I think these may expressions may be of use for the large parameters you speak of, and were going to be my next line of attack.  
  
It's a while since I stopped on this, but I believe at least one of those is in use (the first one, recast in terms of 2F0 see Pearson below), not sure about the other 2 and if they offer anything different.  The main references I've been using are:  
  
NUMERICAL METHODS FOR THE COMPUTATION OF THE CONFLUENT AND GAUSS HYPERGEOMETRIC FUNCTIONS, JOHN W. PEARSON †, SHEEHAN OLVER ‡, AND MASON A. PORTER  
  
Pearson's thesis: Computation of Hypergeometric Functions.  
  
Algorithms for Rational Approximations for A Confluent Hypergeouietric Function, Yudell L. Luke.  
  
Hypergeometric Type Functions and Their Symmetries Jan Derezi´nski  
  
The main problem area that had me stumped was a, b and z all large and negative (or equivalent values from symmetry relations).  If I could shift any one of the parameters either much larger or smaller via recurrence relations then everything would be fine - but the direction of stability of the recurrence relations is itself  unstable!  As you apply the relations repeatedly in any particular direction, you hit a point where they suddenly go crazy and the magnitude and/or sign of the result fluctuates violently and then the direction of stability reverses... even if the relations were consistently unstable at least we could use Millers algorithm, but we can't even rely on that :(  
  
>It looks like your implementation already has a lot of good stuff, so should I just close this PR and start working on your branch or do you see anything of value here (the doc writeup might be used)?  
  
It's pretty complete apart from the one problem area I think, the docs I'm sure could use some improvement.  So yes, by all means see if you can improve on what's in that branch!  
  
In the even that the problem areas can't be "solved", my plan was to just write a checked summation of the series that throws an evaluation_error if it becomes too unstable.  
  
Best, John.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2018-09-03 23:40:23 UTC  
> Url: https://github.com/boostorg/math/pull/147#issuecomment-418205807  

Moving discussion to #148

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2018-09-04 18:32:41 UTC  
> Url: https://github.com/boostorg/math/pull/147#issuecomment-418472403  

This would be a good one to get deeper into in the fall evenings. I looked into Olver's "Asymptotics and Special Functinos", but there is no detailed information about hypergeometric_1f1. I've seen some CAS's do amazing things with hypergeometrics (complex, real argument and parameters, etc.), but I have no idea how they do it. With kind regards, Chris  
   On Monday, September 3, 2018, 7:16:40 PM GMT+2, jzmaddock <notifications@github.com> wrote:    
   
   
  
Have you seen the asymptotics from Handbook of Exact Solutions to Ordinary Differential Equations, S.2.7-6, by Andrei Polyanin? I think these may expressions may be of use for the large parameters you speak of, and were going to be my next line of attack.  
  
  
It's a while since I stopped on this, but I believe at least one of those is in use (the first one, recast in terms of 2F0 see Pearson below), not sure about the other 2 and if they offer anything different. The main references I've been using are:  
  
NUMERICAL METHODS FOR THE COMPUTATION OF THE CONFLUENT AND GAUSS HYPERGEOMETRIC FUNCTIONS, JOHN W. PEARSON †, SHEEHAN OLVER ‡, AND MASON A. PORTER  
  
Pearson's thesis: Computation of Hypergeometric Functions.  
  
Algorithms for Rational Approximations for A Confluent Hypergeouietric Function, Yudell L. Luke.  
  
Hypergeometric Type Functions and Their Symmetries Jan Derezi´nski  
  
The main problem area that had me stumped was a, b and z all large and negative (or equivalent values from symmetry relations). If I could shift any one of the parameters either much larger or smaller via recurrence relations then everything would be fine - but the direction of stability of the recurrence relations is itself unstable! As you apply the relations repeatedly in any particular direction, you hit a point where they suddenly go crazy and the magnitude and/or sign of the result fluctuates violently and then the direction of stability reverses... even if the relations were consistently unstable at least we could use Millers algorithm, but we can't even rely on that :(  
  
  
It looks like your implementation already has a lot of good stuff, so should I just close this PR and start working on your branch or do you see anything of value here (the doc writeup might be used)?  
  
  
It's pretty complete apart from the one problem area I think, the docs I'm sure could use some improvement. So yes, by all means see if you can improve on what's in that branch!  
  
In the even that the problem areas can't be "solved", my plan was to just write a checked summation of the series that throws an evaluation_error if it becomes too unstable.  
  
Best, John.  
  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub, or mute the thread.

---
