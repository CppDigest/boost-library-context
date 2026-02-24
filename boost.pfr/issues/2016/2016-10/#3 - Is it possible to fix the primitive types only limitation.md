# #3 - Is it possible to fix the primitive types only limitation? [Closed]

> Username: aminroosta  
> Created at: 2016-10-12 21:31:33 UTC  
> Updated at: 2017-01-05 17:59:09 UTC  
> Closed at: 2017-01-05 17:59:09 UTC  
> Url: https://github.com/boostorg/pfr/issues/3  

@apolukhin @AntonBikineev  
I've been trying to somehow overcome the limitation for types for a couple of days!  
Here is my failed attempt explained on stackoverflow ( [How to make C++ ADL to look into all instances of a template?](http://stackoverflow.com/questions/40008554/how-to-make-c-adl-to-look-into-all-instances-of-a-template) ).  
  
I have a feeling that there might be a way to do this (it's stopping me from going to sleep every night 😞 ). Please take look at my question, maybe you guys could find something i couldn't see.

---

## Comment 1

> Username: apolukhin  
> Created at: 2016-10-14 06:08:52 UTC  
> Url: https://github.com/boostorg/pfr/issues/3#issuecomment-253717632  

I've already tried to walk that way and failed to reach success :(  
  
Maybe I've missed something, but I'm definitely not going to start playing with friend functions again in the nearest future.

---

## Comment 2

> Username: aminroosta  
> Created at: 2016-10-23 10:19:28 UTC  
> Updated at: 2016-10-23 10:20:05 UTC  
> Url: https://github.com/boostorg/pfr/issues/3#issuecomment-255580435  

@apolukhin Someone found a [solution](http://stackoverflow.com/questions/40008554/how-to-make-c-adl-to-look-into-all-instances-of-a-template?answertab=votes#tab-top) and It's really amazing ❤️ 😃   
  
BUT only works after gcc 5.2, clang 3.5.1 - 3.7.1

---

## Comment 3

> Username: brunocodutra  
> Created at: 2016-12-24 19:25:24 UTC  
> Url: https://github.com/boostorg/pfr/issues/3#issuecomment-269097226  

@aminroosta If I understand it correctly, the snippet I posted on #5 should achieve what you seek

---

## Comment 4

> Username: apolukhin  
> Created at: 2017-01-05 17:59:09 UTC  
> Url: https://github.com/boostorg/pfr/issues/3#issuecomment-270711538  

Solution is integrated into the library :) Limitation removed: non-flat functions work with any aggregate initializable function.
