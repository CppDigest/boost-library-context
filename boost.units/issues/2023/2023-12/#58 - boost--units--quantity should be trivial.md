# #58 - boost::units::quantity should be trivial [Open]

> Username: hlewin  
> Created at: 2023-12-08 11:18:50 UTC  
> Updated at: 2024-02-14 10:55:47 UTC  
> Url: https://github.com/boostorg/units/issues/58  

I think `boost::units::quantity<>` should be as trivial as possible.  
  
Right now they are not ( std::is_trivial_v is false ) because of more-or-less superfluous implementations of constructors and assignment operators. The internally asserted layout compatibilities have no meaning for users of the class.  
  
I do not really have a strong argument for making them trivial but have a strong gut feeling they should be (for users at least).

---

## Comment 1

> Username: correaa  
> Created at: 2023-12-09 05:30:26 UTC  
> Updated at: 2023-12-09 06:35:33 UTC  
> Url: https://github.com/boostorg/units/issues/58#issuecomment-1848245930  

(Disclaimer: I am not a developer of Boost.Units, I am just a user, and I think there are strong arguments for this).  
  
I agree with this.  
I think it is a justified design, but history is not on our side.  
  
For a while, I mistakenly thought that it did make sense for quantities to have a (non-trivial) zero initialization because zero values are, well, special for quantities/measurements.  
With time, I realized that it is not a good reason, mainly from reading the book "Elements of Programming" and some related talks https://www.youtube.com/watch?v=HcSAIZafNZU "Partially-Formed Objects For Fun And Profit".  
  
Many types are unfortunately stuck on being non-trivial when they shouldn't be, for example, std::pair of trivials or even std::complex.  
https://en.cppreference.com/w/cpp/numeric/complex  
Non-trivial std::complex, in particular, is a disaster for people doing high-performance numerics.  
  
I don't know why this problem is so prevalent in old (even standard) libraries; I think the problem is that in C++98, it was not very easy to make types trivial (no `= default;`), so you had to write a lot of class specializations.  
  
This an analogous request I made to have another complex type be trivial: https://github.com/NVIDIA/thrust/issues/1079  
  
And I am afraid what you bring up is exactly the same problem for std::quantity, which I also consider a numeric type.  
The problem I observe is that it is almost impossible to change a type and make a trivial after the fact, considering backward compatibility.  
  
As much as I would love std::quantity to be trivial (especially trivially constructor), I hate to be the bearer of bad news: the problem looking forward is that if some people already relied on zero initialization, this will be a breaking change.  
  
Finally, as another datapoint, note that `std::chrono::duration` (a time quantity implementation), a model for modern C++ classes, **is** trivial: https://godbolt.org/z/Ej8rKEsG5 .

---

## Comment 2

> Username: hlewin  
> Created at: 2023-12-09 15:51:07 UTC  
> Url: https://github.com/boostorg/units/issues/58#issuecomment-1848445408  

There could be ifdefs for such things. There are no necessities. Just gut feelings (hope they ate well).

---

## Comment 3

> Username: NAThompson  
> Created at: 2024-02-10 00:26:04 UTC  
> Url: https://github.com/boostorg/units/issues/58#issuecomment-1936768588  

It indeed should be trivial-without it generic code using atomics does not work:  
  
```  
/opt/homebrew/opt/llvm/bin/../include/c++/v1/__atomic/cxx_atomic_impl.h:315:37: fatal error: _Atomic cannot be applied to type 'boost::units::quantity<boost::units::unit<boost::units::list<boost::units::dim<boost::units::length_base_dimension, boost::units::static_rational<2>>, boost::units::dimensionless_type>, boost::units::homogeneous_system<boost::units::list<boost::units::si::meter_base_unit, boost::units::list<boost::units::scaled_base_unit<boost::units::cgs::gram_base_unit, boost::units::scale<10, static_rational<3>>>, boost::units::list<boost::units::si::second_base_unit, boost::units::list<boost::units::si::ampere_base_unit, boost::units::list<boost::units::si::kelvin_base_unit, boost::units::list<boost::units::si::mole_base_unit, boost::units::list<boost::units::si::candela_base_unit, boost::units::list<boost::units::angle::radian_base_unit, boost::units::list<boost::units::angle::steradian_base_unit, boost::units::dimensionless_type>>>>>>>>>>>>' which is not trivially copyable  
  315 |   _LIBCPP_DISABLE_EXTENSION_WARNING _Atomic(_Tp) __a_value;  
      |                                     ^  
```

---

## Comment 4

> Username: NAThompson  
> Created at: 2024-02-10 01:30:42 UTC  
> Url: https://github.com/boostorg/units/issues/58#issuecomment-1936796953  

@jhunold , @swatanabe : Any hope for getting this patchfile accepted?  
  
[make_trivial.patch](https://github.com/boostorg/units/files/14227412/make_trivial.patch)

---

## Comment 5

> Username: hlewin  
> Created at: 2024-02-10 10:22:26 UTC  
> Url: https://github.com/boostorg/units/issues/58#issuecomment-1936964400  

If changing behaviour is not an option at all, I'd suggest introducing new namespace for such fixes.

---

## Comment 6

> Username: NAThompson  
> Created at: 2024-02-10 12:27:08 UTC  
> Url: https://github.com/boostorg/units/issues/58#issuecomment-1936995114  

@hlewin : What behavior is changed?

---

## Comment 7

> Username: correaa  
> Created at: 2024-02-10 21:24:54 UTC  
> Updated at: 2024-02-10 21:27:02 UTC  
> Url: https://github.com/boostorg/units/issues/58#issuecomment-1937179201  

@NAThompson, as I said (in my convoluted comment), the current behavior initializes the value to zero. A proper trivial type doesn't initialize to zero; it "leaves" the value uninitialized.   
  
If someone's code relied on that default initialization producing a zero value, it would be broken by making the type trivial.  
  
It is unfortunate that quantity was not trivial from the start.   
  
I had a similar dilemma concerning changing behavior.   
I made similar changes in an internal version of B.Units.   
For example, in most cases, I really wanted implicit conversion between compatible units.   
  
For that, I have boost::units::implicit::quantity, which, in practice, I use as   
  
```  
void(implicit:: quantity<length>)  
```  
  
Analogously, I think what @hlewin is saying is that you can have: `boost::units::trivial::quantity` with your desired behavior.

---

## Comment 8

> Username: hlewin  
> Created at: 2024-02-10 23:51:16 UTC  
> Url: https://github.com/boostorg/units/issues/58#issuecomment-1937357305  

> Analogously, I think what @hlewin is saying is that you can have: `boost::units::trivial::quantity` with your desired behavior.  
  
The problem being that `boost::quantity` would need to inherit from `boost::trivial::quantity` - not the other way around.

---

## Comment 9

> Username: correaa  
> Created at: 2024-02-11 00:30:58 UTC  
> Url: https://github.com/boostorg/units/issues/58#issuecomment-1937370377  

Yes, I agree.  
  
I was referring to the fact of using a new class only.   
  
That is why trivializing a class after the fact is so difficult.

---

## Comment 10

> Username: hlewin  
> Created at: 2024-02-11 08:39:12 UTC  
> Url: https://github.com/boostorg/units/issues/58#issuecomment-1937475102  

> Yes, I agree.  
>   
> I was referring to the fact of using a new class only.  
>   
> That is why trivializing a class after the fact is so difficult.  
  
It is not: Declare the old behaviour deprecated and provide a define like `BOOST_REMOVE_DEPRECATED` to get rid of the constructors on a voluntary basis.

---

## Comment 11

> Username: correaa  
> Created at: 2024-02-11 10:24:45 UTC  
> Url: https://github.com/boostorg/units/issues/58#issuecomment-1937528452  

@hlewin I couldn't agree more that quantity<T> should be trivial if T is trivial. I am just saying that it would be a breaking change. Moreover, I would agree with the breaking change if it were in my hands.

---

## Comment 12

> Username: hlewin  
> Created at: 2024-02-11 10:31:51 UTC  
> Url: https://github.com/boostorg/units/issues/58#issuecomment-1937536929  

> @hlewin I couldn't agree more that quantity should be trivial if T is trivial. I am just saying that it would be a breaking change. Moreover, I would agree with the breaking change if it were in my hands.  
  
Yeah, point is: Writing a patch to whatever spec is not really a problem. But I am not gonna waste that time if the general sentiment is to not accept it one way or the other. If there are arguments like "someone might have taken the sha256 checksum of that header so changing it might break a build" I am just outta here. I already have this patched in company local versions.

---

## Comment 13

> Username: correaa  
> Created at: 2024-02-11 10:37:07 UTC  
> Updated at: 2024-02-11 10:40:27 UTC  
> Url: https://github.com/boostorg/units/issues/58#issuecomment-1937543393  

@hlewin , well, let's see what the developers say, if they say something. (I am not a developer or maintainer of Boost.Units).   
  
I have my own <s>patches</s> add-ons, too.  
These are the public ones https://gitlab.com/correaa/boost-unitx (including `implicit:: quantity`).

---

## Comment 14

> Username: NAThompson  
> Created at: 2024-02-11 17:59:08 UTC  
> Url: https://github.com/boostorg/units/issues/58#issuecomment-1937823486  

My own preference would be to make a breaking change, because well, std::atomic doesn't work with these types due to the triviality requirements, and therefore much of my multithreaded code simply doesn't compile with these types.

---

## Comment 15

> Username: hlewin  
> Created at: 2024-02-12 07:16:41 UTC  
> Updated at: 2024-02-12 07:17:21 UTC  
> Url: https://github.com/boostorg/units/issues/58#issuecomment-1938156018  

Discussing the technicalities of a fix would only make sense if there would be consensus about that this is a problem with someone who can make such decisions. I don't see that. Zero-initialization of numerical values was a javaesque design decision back then and did not age well on the c++ side either.  So... I don't hold my breath.  
Have a nice day!

---

## Comment 16

> Username: swatanabe  
> Created at: 2024-02-13 23:48:49 UTC  
> Url: https://github.com/boostorg/units/issues/58#issuecomment-1942865590  

AMDG  
  
On 2/12/24 00:16, Heiko Lewin wrote:  
> Discussing the technicalities of a fix would only make sense if there would consensus about that this is a problem with someone who can make such decisions. I don't see that. Zero-initialization of numerical values was a javaesque design decision back then and did not age well on the c++ side either.  So... I don't hold my breath.  
  
Since =default didn't exist back in 2006, and quantity has other  
constructors, the choice was between having no default constructor,  
having the default constructor zero-initialize, or allowing  
value-initialization to leave the object uninitialized.  
Zero-initialization was the least harmful option at the time.  
Making it trivial is obviously the right choice if we were  
implementing it from scratch today, but I'm very hesitant to  
make such a change, as it would silently cause previously  
correct code have undefined behavior.  
  
With that being said, the copy constructor, destructor,  
and copy-assignment operators definitely can and should  
be defaulted.  
  
In Christ,  
Steven Watanabe

---

## Comment 17

> Username: hlewin  
> Created at: 2024-02-14 07:56:10 UTC  
> Url: https://github.com/boostorg/units/issues/58#issuecomment-1943243308  

> Making it trivial is obviously the right choice if we were implementing it from scratch today, but I'm very hesitant to make such a change, as it would silently cause previously correct code have undefined behavior.  
  
Those concerns can be addressed though, ie the existing behaviour could be preserved for legacy code with a maintenance cost close to zero.  
  
First I already mentioned the possibility of utilizing defines and/or deprecation declarations.   
But I think the more elegant solution is the second one.   
  
So from the top of my head I'd propose the following:  
1. provide a class `trivial_quantity` that contains all the functionality of quantity except the constructors and use that as base class for quantity. This should mostly preserve the behaviour of `quantity`.  
From here there are different ways to proceed.  
  
2a) At the very least it should now possible for a user of the lib to provide specialisations of `quantity` without basically copying the complete source-code. Such specialisations could, of course, potentially leak through dependency chains of the eco-system, but then - this is always the case already.  
2b) Such a specialisation for trivial types could be provided in an official, optional-to-include header file. This optionally could also provide ways to more easily find usages of the old behaviour to ease migration by utilising deprecation-attributes: It is safe to say, that, for existing legacy code any invocation of the default-constructor would have to be reviewed. So providing an empty default constructor with a deprecation attribute should point users to places to review, I guess. This could also be used for other approaches to the problem btw.  
2c) Maybe `trivial_quantity` could also be used directly by making it playing nicely with the rest of the lib. Either with overloads (much work, but cleaner in a way) or by making it properly auto-convert to and from `(const) quantity(&)`.  
  
Depending on the approach taken one could discuss how and to what extend the library and dependent source-code should be steered towards "the right way".

---

## Comment 18

> Username: NAThompson  
> Created at: 2024-02-14 10:55:46 UTC  
> Url: https://github.com/boostorg/units/issues/58#issuecomment-1943525461  

> Making it trivial is obviously the right choice if we were  
implementing it from scratch today, but I'm very hesitant to  
make such a change, as it would silently cause previously  
correct code have undefined behavior.  
  
My impression is that serious users slowly discover all the problems with this library, then either give up or fork the repo-which seems sad given how little effort it would take to fix these small problems.  
  
I'd be very curious to see how many users actually exist and how many give up due to inability to compile large codebases on this type.  
  
IMO the deprecation warning is the way to go and then make a breaking change in 2-3 releases.
