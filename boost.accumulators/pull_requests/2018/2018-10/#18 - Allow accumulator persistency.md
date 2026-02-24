# #18 Allow accumulator persistency [Merged]

> Username: yuvalif  
> Created at: 2018-10-31 21:17:27 UTC  
> Updated at: 2019-05-10 22:40:01 UTC  
> Merged at: 2019-05-10 22:39:28 UTC  
> Closed at: 2019-05-10 22:39:28 UTC  
> Url: https://github.com/boostorg/accumulators/pull/18  

This PR is meant to address [this](https://stackoverflow.com/questions/32289719/how-to-serialize-boostaccumulatorsaccumulator-set) request (also tracked as issue #13).  
The great value from accumulators is the fact there is no need to store large amounts of raw data, in order to get the statistics. Allowing serialization of internal state, so that the process accumulating the stats, could stop and continue from where it left off, would allow for more scenarios to use accumulators without the need to store the raw data.  
> 1. there should be no perf impact on regular operation of the accumulators  
> 2. ~~rolling stats are not serializeable yet (more work is needed for serializing the circular buffer)~~  
> 3. In some cases, the ctor parameters are stored inside the accumulator, so, when deserialized they are overwritten. Was not sure if this is what would be the expected behavior?  
> 4. made some loops longer to fix flaky tests (unrelated to this PR), addressing issue #12

---

## Comment 1

> Username: ericniebler  
> Created_at: 2018-11-01 20:48:19 UTC  
> Url: https://github.com/boostorg/accumulators/pull/18#issuecomment-435181874  

VERY cool. I don't have time to fully review right now. I'm a little concerned about "rolling stats are not serializeable yet". Should I give you more time to finish this up?

---

## Comment 2

> Username: yuvalif  
> Created_at: 2018-11-04 07:50:41 UTC  
> Url: https://github.com/boostorg/accumulators/pull/18#issuecomment-435649596  

@ericniebler will gladly add that. However, to prevent dependencies with a PR to boots/serialization, I'll just add the circular buffer serialization support here.  
Will try to push that as a PR to the serialization repo as a separate effort (and maybe converge later).

---

## Comment 3

> Username: yuvalif  
> Created_at: 2018-11-09 07:36:15 UTC  
> Updated_at: 2019-01-31 07:29:27 UTC  
> Url: https://github.com/boostorg/accumulators/pull/18#issuecomment-437275253  

added rolling stats persistency and tests.  
boost::circular_buffer serialization was added locally to prevent dependency issues. Started a PR (https://github.com/boostorg/circular_buffer/pull/25) on the serialization repo for the circular buffer so that the code could end up eventually in the right place.  
will remove from here, once the other PR is merged.

---

## Comment 4

> Username: redlars  
> Created_at: 2019-05-10 11:28:47 UTC  
> Url: https://github.com/boostorg/accumulators/pull/18#issuecomment-491256287  

This is a very interesting feature.   
  
What is the status of this work and when is it planned to be included in a Boost release?

---

## Comment 5

> Username: yuvalif  
> Created_at: 2019-05-10 12:42:13 UTC  
> Url: https://github.com/boostorg/accumulators/pull/18#issuecomment-491275194  

@redlars the work on this feature is complete, and waiting for review.  
The only point for discussion is for the circular buffer serialization. In this PR is is done within the specific context of accumulators (the rolling window accumulators use it).  
In parallel I started another PR, for more general circular buffer serialization. However, It does not seem like there is an agreement on the necessity of that feature in the general case for circular buffer.  
So, I would prefer to accept this PR as-is, and modify the code, in the future, if circular buffer serialization will be added to the circular buffer code.

---

## Comment 6

> Username: ericniebler  
> Created_at: 2019-05-10 22:40:01 UTC  
> Url: https://github.com/boostorg/accumulators/pull/18#issuecomment-491449592  

I've merged this. Apologies for the lag. Accumulators currently lacks an active maintainer.

---
