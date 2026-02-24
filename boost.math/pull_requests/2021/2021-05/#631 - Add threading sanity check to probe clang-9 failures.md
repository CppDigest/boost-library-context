# #631 Add threading sanity check to probe clang-9 failures. [Merged]

> Username: jzmaddock  
> Created at: 2021-05-17 10:33:09 UTC  
> Updated at: 2021-05-19 15:57:22 UTC  
> Merged at: 2021-05-19 15:01:29 UTC  
> Closed at: 2021-05-19 15:01:29 UTC  
> Url: https://github.com/boostorg/math/pull/631  

See: https://github.com/boostorg/math/pull/629.

---

## Comment 1

> Username: mborland  
> Created_at: 2021-05-17 15:37:51 UTC  
> Url: https://github.com/boostorg/math/pull/631#issuecomment-842426081  

So clang 9 looks broken. Same errors with the spaceship operator in the C++20 runs, and your sanity check segfaults.

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2021-05-17 15:47:36 UTC  
> Updated_at: 2021-05-17 15:49:12 UTC  
> Url: https://github.com/boostorg/math/pull/631#issuecomment-842433166  

> Same errors with the spaceship operator in the C++20 runs  
  
If this helps, I also found this phenomenon in another completely unrelated project and simply removed clang 9 runs on C++20. The compiler kept mentioning something about the spaceship operator in its STL, so I did not want to deal with it at all and removed the runs from that particular project.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-05-17 19:31:49 UTC  
> Url: https://github.com/boostorg/math/pull/631#issuecomment-842577543  

This is a bit of a bloodbath - @ckormanyos it's not just C++20, my simple Boost-free multi threading test segfaults in C++17 too.  
  
Since this all used to work, I assume CI has updated something on the ubuntu image, we have it fixed to ubuntu-20.04 though, so I would expect this to be OK?  Plus, I've just checked my Ubunutu VirtualBox and that's 20.04 and up to date and seems to work OK.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2021-05-17 20:44:02 UTC  
> Updated_at: 2021-05-17 20:45:03 UTC  
> Url: https://github.com/boostorg/math/pull/631#issuecomment-842625700  

> assume CI has updated something on the ubuntu image  
  
That rings a bell @jzmaddock. To be honest, all of a sudden, my nightly build of real-time-c++ companion code stopped working. The culprit was something spaceship operator/STL/clang-9. The day of the changed nightly build is shown in the pic below. Instead of investigating the problem, I eliminated the offending compiler runs.  
  
I also got the impression that something in the CI machine changed. But i side stepped the problem before even really looking into it.  
  
I can dive a bit deeper into the nature of what's really going on.  
  
![grafik](https://user-images.githubusercontent.com/2404721/118553926-3a22c500-b761-11eb-9bb6-bb7957895bae.png)

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2021-05-17 20:50:26 UTC  
> Url: https://github.com/boostorg/math/pull/631#issuecomment-842629410  

Here is an [example of errors](https://github.com/ckormanyos/real-time-cpp/runs/2581454568?check_suite_focus=true#step:5:48) that i was getting on that day. I can see what's similar with the failures here.

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2021-05-17 20:52:40 UTC  
> Updated_at: 2021-05-17 20:53:35 UTC  
> Url: https://github.com/boostorg/math/pull/631#issuecomment-842630839  

The errors are the same. The ones I got and the ones in this project.  
  
It starts off with  
  
```  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/chrono:1345:24: error: unknown type name 'strong_ordering'  
```  
  
Then the compiler moves on to comparison failures related to what appear to be the units of time epochs with is-convertable and maybe the the spaceship operator of generalized equality.  
  
I can also try and take a closer look

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2021-05-17 20:56:11 UTC  
> Updated_at: 2021-05-17 20:58:40 UTC  
> Url: https://github.com/boostorg/math/pull/631#issuecomment-842633056  

Sorry for going on, but I need to provide what I do know. I need to be more specific, the failures that seem to be in the latest run here and those that I encountered elsewhere were clang-8 and clang-9 with C++2a.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2021-05-18 08:25:22 UTC  
> Url: https://github.com/boostorg/math/pull/631#issuecomment-842966435  

I think I can tell you what the difference is, but not *why*.  
  
clang-9 is passing on drone: https://drone.cpp.al/boostorg/math/212/33/2  
Same configuration and Ubuntu version is failing on GHA: https://github.com/boostorg/math/pull/631/checks?check_run_id=2601142494  
  
The latter has GNU libstdc++ version 20210427 the former GNU libstdc++ version 20191114 and not surprisingly, clang-9 can't cope with a libstdc++ released well after it was.  I'm not sure which gcc version 20210427 corresponds to, but it doesn't appear to be a gcc-9 flavour which is what I would have expected.  Even the gcc-10 builds have GNU libstdc++ version 20200808 on GHA.  
  
I think since we have clang-9 working on drone, we'll just remove it from GHA?

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2021-05-18 10:29:43 UTC  
> Url: https://github.com/boostorg/math/pull/631#issuecomment-843053797  

> The latter has GNU libstdc++ version 20210427 the former GNU libstdc++ version 20191114 and not surprisingly, clang-9 can't cope with a libstdc++ released well after it was. I'm not sure which gcc version 20210427 corresponds to, but it doesn't appear to be a gcc-9 flavour which is what I would have expected. Even the gcc-10 builds have GNU libstdc++ version 20200808 on GHA.  
  
Great description @jzmaddock that fits perfectly with my empirical observations as well.  
  
One strange point is that this change appears to have happened on GHA literally _overnight_ a little less than a week ago.  
  
> I think since we have clang-9 working on drone, we'll just remove it from GHA?  
  
Yes. I would recommend to simply remove the offending compiler configurations, otherwise you're just testing the setup of the compiler(s) instead of the actual code.  
  
Some folks actually make docker images of known-to-work compiler topologies for this exact reason, but i really think this is beyond the scope of work that I could invest in this CI.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2021-05-18 10:43:24 UTC  
> Url: https://github.com/boostorg/math/pull/631#issuecomment-843061849  

>One strange point is that this change appears to have happened on GHA literally overnight a little less than a week ago.  
  
The images are updated weekly with the latest package releases.  It's just weird I can't reproduce locally.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2021-05-18 10:44:29 UTC  
> Url: https://github.com/boostorg/math/pull/631#issuecomment-843062491  

I forgot to say, drone images are updated less often, but they do get updated, we may yet see the same issue appear there, which would be super annoying :(

---
