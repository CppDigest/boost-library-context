# #16 Deleted code marked as "shouldn't be here - included for backward com… [Closed]

> Username: robertramey  
> Created at: 2017-06-19 17:44:05 UTC  
> Updated at: 2017-06-26 17:41:00 UTC  
> Closed at: 2017-06-26 17:41:00 UTC  
> Url: https://github.com/boostorg/rational/pull/16  

…patibility only"  
  
This was giving me fits while trying to use rational<safe<int>>  
I don't know what other implications this deletion might have.

---

## Comment 1

> Username: robertramey  
> Created_at: 2017-06-22 16:43:18 UTC  
> Url: https://github.com/boostorg/rational/pull/16#issuecomment-310436498  

@Ping - Could someone take a look at this and decide whether or not to merge it in?  I'm sort of depending on this.

---

## Comment 2

> Username: eldiener  
> Created_at: 2017-06-22 17:03:14 UTC  
> Url: https://github.com/boostorg/rational/pull/16#issuecomment-310441639  

I have two hesitatiions:  
  
1) Should we not somehow deprecate for at least one release, via release notes, before actually removing ?  
2) Does any other Boost library use what we want to remove ?

---

## Comment 3

> Username: robertramey  
> Created_at: 2017-06-22 17:15:21 UTC  
> Url: https://github.com/boostorg/rational/pull/16#issuecomment-310444658  

On 6/22/17 10:03 AM, Edward Diener wrote:  
>  
> I have two hesitatiions:  
>  
>  1. Should we not somehow deprecate for at least one release, via  
>     release notes, before actually removing ?  
>  2. Does any other Boost library use what we want to remove ?  
>  
These are my exact concerns.  I was hoping one of you guys might know   
the answer.  The comment strongly suggests that the code shouldn't be   
there.  Looking at it seems that it would conflict with   
rational<boost:multiprecision_integer> as well as rational<safe<int>>.    
This seems to conflict with stated intention in the documents of   
permitting just such usage.  I confess, I didn't look into it all that   
deeply as I'm currently frying my own fish.  
  
In researching to make this comment I came up with this   
https://github.com/robertramey/safe_numerics/issues/34  which I had   
totally forgotten about.  I had made my own example by tweakng the   
orginal example from the rational tutorial.  So I guess I can't avoid   
doing some more homework.  But still the questions stand.  
  
--   
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2017-06-22 17:19:30 UTC  
> Url: https://github.com/boostorg/rational/pull/16#issuecomment-310445775  

As the person who put that code in there, can I ask why it's necessary to remove it?  
  
I ask because the code was provided to ensure backwards compatibility with the original Boost.Rational, where `rational<T>` was constructible from any type implicitly convertible to T.  Really gross mistakes like constructing `rational<int>` from `float `have been outlawed, but this allows `rational<int>` to be constructed from say a` long long`, along with error checking.  One could argue that this was a design error in the original Boost.Rational, not to mention a complete lack of testing what the docs claimed should happen.  But we are where we are.  And, yes, I'm fairly sure removing this will break some users code.

---

## Comment 5

> Username: robertramey  
> Created_at: 2017-06-22 17:49:21 UTC  
> Url: https://github.com/boostorg/rational/pull/16#issuecomment-310453454  

On 6/22/17 10:19 AM, jzmaddock wrote:  
>  
> As the person who put that code in there, can I ask why it's necessary   
> to remove it?  
>  
I'm trying to make safe<int> work with rational.hpp.  I've had   
unexpected difficulties.  My problems disappeared when I eliminated this   
code.  I'm guessing that there is some difficult with construction of a   
rational from a pair of safe integers.  I sort of jumped to the   
conclusion that the same problem would occur with the construction of a   
rational from a pair of multi-precision integers. The comments gave me   
the impression that piece of code would no longer be necessary.  Oh   
well.  I guess I just have to do more homework.  
>  
> I ask because the code was provided to ensure backwards compatibility   
> with the original Boost.Rational, where |rational<T>| was   
> constructible from any type implicitly convertible to T. Really gross   
> mistakes like constructing |rational<int>| from |float|have been   
> outlawed, but this allows |rational<int>| to be constructed from say   
> a|long long|, along with error checking. One could argue that this was   
> a design error in the original Boost.Rational, not to mention a   
> complete lack of testing what the docs claimed should happen. But we   
> are where we are. And, yes, I'm fairly sure removing this will break   
> some users code  
>  
OK - this is very helpful.  
  
--   
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 6

> Username: robertramey  
> Created_at: 2017-06-26 17:41:00 UTC  
> Url: https://github.com/boostorg/rational/pull/16#issuecomment-311130096  

OK - I'm wrong about this.  This was actually something quite unrelated in my own code  which manifested itself here.  Thanks for helping me clear this up.

---
