# #288 Feature: New algorithms relate() and relation() [Merged]

> Username: awulkiew  
> Created at: 2015-05-01 17:01:00 UTC  
> Updated at: 2015-05-26 17:02:15 UTC  
> Merged at: 2015-05-26 15:27:52 UTC  
> Closed at: 2015-05-26 15:27:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/288  

This PR adds the OGC SFA Relate functionality. The use case looks as follows:  
  
```  
// within  
bool a = bg::relate(geom1, geom2, bg::de9im::mask("T*F**F***"));  
// touches  
bool b = bg::relate(geom1, geom2, bg::de9im::mask("FT*******")  
                               || bg::de9im::mask("F**T*****")  
                               || bg::de9im::mask("F***T****"));  
```  
  
Masks can also be create at compile-time (they can also be ORed with `operator||`):  
  
```  
bool c = bg::relate(geom1, geom2,  
                    bg::de9im::static_mask<'T','*','F','*','*','F' /* *** */>());  
```  
  
This PR also adds a non-standard function caluclating the relation for a pair of geometries:  
  
```  
bg::de9im::matrix m = bg::relation(geom1, geom2);  
```  
  
It's implemented as a separate function because IMO it's more clear. Furthermore in case if we wanted to add a Strategy in the future and if both variants had the same name `relate` then the third argument would have to be `MaskOrStrategy` which could be confusing.  
  
The documentation for both functions is added in the "Algorithms" section with examples and a picture (taken from within). A new section DE9IM is added to the Reference and now contains `de9im::matrix`, `de9im::mask` and `de9im::static_mask`.

---

## Comment 1

> Username: meastp  
> Created_at: 2015-05-04 05:50:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#issuecomment-98593688  

That's a nice and easy to use interface :)

---

## Comment 2

> Username: mloskot  
> Created_at: 2015-05-04 08:11:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#issuecomment-98624373  

:+1:

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-05-11 15:46:00 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30050861  

I think we typically use `_type` in such cases. So `base_t` should rather be `base_type` I think.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-05-11 15:48:29 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30051136  

Why is this defined as a typedef, while `static_mask_equals_type` via a struct?  
I think it would be better to have a unified way for defining these static masks.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-05-11 15:50:26 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30051364  

Please either remove these comments, or explain why they are commented (possibly with a `TODO` explanation).

---

## Comment 6

> Username: mkaravel  
> Created_at: 2015-05-11 15:53:04 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30051695  

I do not particularly like the name `relate_base` (I think we rarely use `_base` in names). Maybe `relate_impl`?

---

## Comment 7

> Username: mkaravel  
> Created_at: 2015-05-11 15:54:55 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30051935  

Same comment here about `base_t`.

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-05-11 15:56:21 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30052097  

Why keeping this commented?

---

## Comment 9

> Username: mkaravel  
> Created_at: 2015-05-11 16:00:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#issuecomment-100957708  

@awulkiew Thanks for the new functionality!!!  
  
What I would really like to see is predefined documented masks that would be accessible by the user. To be more precise, as a user I would like to be able to write something like `bg::de9im::static_mask_covered_by` or `bg::de9im::mask_covered_by` (I think we need both, but I am not 100% sure) and use it with `relate()`.  
What do you think?

---

## Comment 10

> Username: awulkiew  
> Created_at: 2015-05-11 18:19:45 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30066450  

I think because disjoint/intersects doesn't use relate so I just ommited this one. Another good explanation could be laziness :) Even if those are details I guess those predefined masks indeed should probably have the same interface.

---

## Comment 11

> Username: awulkiew  
> Created_at: 2015-05-11 18:21:33 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30066618  

I started implementing them but didn't finished because for this we should first agree how should the Box having the same min and max corner points be treated.

---

## Comment 12

> Username: awulkiew  
> Created_at: 2015-05-11 18:24:16 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30066913  

Yes, I think it could be removed though I'd like to hear from Barend what I should do with it because AFAIU he was the one who contributed this code.

---

## Comment 13

> Username: awulkiew  
> Created_at: 2015-05-11 18:36:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#issuecomment-101012040  

Those masks could be added. Static versions are already in the details. The interface would have to be more complicated because a mask can depend on the dimensionality of the Geometries, so e.g. be different for Linestrings than for Polygons. That said, I'm not sure if they'd be needed since we have other algorithms for this. I think we could keep them in the details unless some user explicitly requested them.  
  
Furthermore I think we could consider adding a mechanism for "full" run-time composition (ORing) of masks. Currently the user must pass them and connect together with `operator||`. This way he can connect only as many mask as there are in the expression. In other words the number of the masks is defined at compile-time. The most naiive thing to do would be to allow passing a Range of masks. But in this case the connection between masks stored in a range wouldn't be so straighforward. We could also define our own run-time `de9im::or` container but I'd prefer something more elegant.

---

## Comment 14

> Username: mkaravel  
> Created_at: 2015-05-11 21:46:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#issuecomment-101058377  

Regarding the interface, I guess you only need to templatize the mask by the geometries. Less elegant than what I original thought, but still, I think, useful. Not to mention that this way it will be easier at either compile time or run-time disallow masks that are not defined for certain geometry combinations.  
  
Other than that, I am okay with leaving it aside for now until a concrete need for this appear.  
I also like the idea of run-time composition of masks.

---

## Comment 15

> Username: mkaravel  
> Created_at: 2015-05-11 21:48:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#issuecomment-101058770  

BTW, the reason I proposed the masks at the user level is very simple: I wanted to have an easy interface through `relate()` and `relation()` to implement relational operations. Given such an interface I would have a hard time remembering the DE9IM definitions of the standard relations, so it would make sense to me that we provide these as part of the functionality of `relate()` and `relation()`.

---

## Comment 16

> Username: awulkiew  
> Created_at: 2015-05-12 12:08:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#issuecomment-101253257  

Yes, I understand your concerns. I've unified the predefined static masks. I prefer to keep them in the details for now. We should be able to easily move them if users requested it. Besides I think that the run-time predefined masks could probably be more useful for some of the users. Instead of using static ones they could just call an algorithm.

---

## Comment 17

> Username: mkaravel  
> Created_at: 2015-05-12 12:56:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#issuecomment-101266306  

@awulkiew Okay, let's leave them in details for now.  
Thanks for taking care of my other comments.

---

## Comment 18

> Username: mkaravel  
> Created_at: 2015-05-12 15:12:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#issuecomment-101316296  

I am okay with merging this PR.

---

## Comment 19

> Username: barendgehrels  
> Created_at: 2015-05-13 14:28:53 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30235616  

Maybe you want to update the copyright ;-)

---

## Comment 20

> Username: barendgehrels  
> Created_at: 2015-05-13 14:29:38 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30235708  

curly brace goes on next line

---

## Comment 21

> Username: barendgehrels  
> Created_at: 2015-05-13 14:29:58 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30235739  

/algorithms/detail ? or util ?

---

## Comment 22

> Username: barendgehrels  
> Created_at: 2015-05-13 14:35:08 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30236282  

I don't know why suddenly all braces not go on next line, but this is not the style of the library...

---

## Comment 23

> Username: barendgehrels  
> Created_at: 2015-05-13 14:37:06 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30236477  

Yes, I think so, already 7 or 8 years ago.  
I'm very fine with removing it now.  
  
The new code looks very good.

---

## Comment 24

> Username: barendgehrels  
> Created_at: 2015-05-13 14:37:51 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30236558  

What does xxx mean?   
Should it be renamed to e.g. "other" ?

---

## Comment 25

> Username: barendgehrels  
> Created_at: 2015-05-13 14:38:21 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30236606  

indentation looks wrong

---

## Comment 26

> Username: barendgehrels  
> Created_at: 2015-05-13 14:39:49 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30236771  

This looks very good!

---

## Comment 27

> Username: barendgehrels  
> Created_at: 2015-05-13 14:41:51 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30237017  

This looks very good too.  
  
Minor note:  
.str() is compatible with std::ostringstream  
Boost.Filesystem uses .string()  
  
Don't know what to prefer.

---

## Comment 28

> Username: barendgehrels  
> Created_at: 2015-05-13 14:42:52 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30237144  

What is this for? To generate documentation?

---

## Comment 29

> Username: barendgehrels  
> Created_at: 2015-05-13 14:44:29 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30237349  

What if people specify a non-recognized pattern? For example:   
static_mask<'A', 'B', 'C'>  
do they get a compile time error-message?

---

## Comment 30

> Username: barendgehrels  
> Created_at: 2015-05-13 14:47:20 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30237719  

Looks good, but where is this (D1LessD2) used? Don't see any usage  
EDIT: oh I see, it's for the specialization

---

## Comment 31

> Username: barendgehrels  
> Created_at: 2015-05-13 14:51:03 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30238169  

Do we use the suffix _impl more often?  
There is also an impl folder in projections, so it is not the only usage of this abbreviation  
But is there not an alternative? Thought I saw already some correspondence about this going by but cannot find it in this PR

---

## Comment 32

> Username: barendgehrels  
> Created_at: 2015-05-13 14:52:35 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30238337  

inline is missing (it is a hpp)

---

## Comment 33

> Username: barendgehrels  
> Created_at: 2015-05-13 14:52:55 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30238384  

same here and below

---

## Comment 34

> Username: barendgehrels  
> Created_at: 2015-05-13 14:54:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#issuecomment-101701801  

Thanks a lot - it looks very good.  
I like the separate relation function  
  
I'm OK with merging this.

---

## Comment 35

> Username: awulkiew  
> Created_at: 2015-05-13 15:10:21 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30240529  

Indeed, `util` would be a better place. I'll do it aside from this PR.

---

## Comment 36

> Username: awulkiew  
> Created_at: 2015-05-13 15:12:38 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30240807  

Sorry, missed this one during refactoring.

---

## Comment 37

> Username: awulkiew  
> Created_at: 2015-05-13 15:15:53 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30241193  

`xxx` stands for "any geometry" so I'll change the name to `relate_pointlike_geometry`.

---

## Comment 38

> Username: awulkiew  
> Created_at: 2015-05-13 15:26:53 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30242442  

I picked `str()` because it is in the standard, and corresponds to `c_str()`.  
We could have both but it's probably not required.

---

## Comment 39

> Username: barendgehrels  
> Created_at: 2015-05-13 15:28:56 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30242698  

One is indeed enough.  
I'm OK with str()

---

## Comment 40

> Username: awulkiew  
> Created_at: 2015-05-13 15:29:50 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30242811  

Yes, it's for documentation. Doxygen is able to copy the docs of the members of the base class only if those members are virtual functions. I don't like the approach I've used but I don't see any other way to have the members in the documentation.

---

## Comment 41

> Username: awulkiew  
> Created_at: 2015-05-13 15:38:40 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30243873  

In general the result is undefined but in practice AFAIR other characters than `F`, `T` and [`0`, `9`] are treated as `*`.

---

## Comment 42

> Username: awulkiew  
> Created_at: 2015-05-13 15:44:10 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30244561  

It's sometimes used, yes. I have nothing against changing that name, previously it was `relate_base`. In this file there is a base class for a specialization/dispatched algorithm which could be implemented by falling back to relate. So it could e.g. be `algorithm_relate` or `relate_dispatch_base` but I don't have an idea which I think is the best. If you have an idea for a better name I'll change it.

---

## Comment 43

> Username: barendgehrels  
> Created_at: 2015-05-13 15:45:07 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30244679  

I think it is possible and not so difficult to give a compile time error.  
  
Something like:  
  
```  
template <char C>  
struct check_char : not_implemented < ... > {};  
  
// Specialize what should compile (maybe give it an apply message)  
template<> struct check_char<'*'> {} ;  
template<> struct check_char<'T'> {} ;  
template<> struct check_char<'F'> {} ;  
```  
  
and then in this struct:  
  
```  
check_char<II>::apply(); // with a static apply - but maybe just a declaration is fine too  
check_char<IB>::apply();  
```  
  
etc  
  
or something like that (I did not try it, but I ever did an experiment with a similar thing)

---

## Comment 44

> Username: awulkiew  
> Created_at: 2015-05-13 15:48:02 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30244997  

It's for unit testing only. We don't use `inline` keyword in the test hpp files.

---

## Comment 45

> Username: barendgehrels  
> Created_at: 2015-05-13 15:53:31 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30245597  

Indeed. Just checked test_buffer and there it is never done...  
In general I think a free function in any hpp should be inlined, so maybe I will change buffer too.  
But as long as the hpp is used in one cpp at at time it should be no problem.

---

## Comment 46

> Username: awulkiew  
> Created_at: 2015-05-13 16:05:43 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30246849  

Of course it would be possible to generate a compiler error but do you think that it should be done? The same question is valid for run-time mask. Should the characters be checked in run-tme and an exception generated? Basically I wanted to avoid additional checks and exceptions, and to be consistent with run-time masks I decided to also not check the characters for compile-time masks. Do you think that the masks should be checked?  
  
Actually implementation could be more generic because internally a `static_mask` uses `boost::mpl::vector_c` of chars.  
  
Btw, AFAIR in DE9IM the specific dimensions numbers can be stored in masks too.

---

## Comment 47

> Username: barendgehrels  
> Created_at: 2015-05-13 16:11:08 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30247451  

> Do you think that the masks should be checked?  
  
Of course... it was actually the first question I did have about the new interface. I doubted between suggesting an enum (because there you cannot specify unsupported options) or leave it as you proposed it. I like the proposal because it is clear, but I think the options should be checked at compiletime (if possible) and at runtime to avoid undefined behaviour.  
  
Yes, using the vector_c functionality avoids checking them individually, agreed

---

## Comment 48

> Username: awulkiew  
> Created_at: 2015-05-13 19:16:16 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30265563  

Ok, another thing. Currently if there is not enough elements (either template parameters or run-time string/array elements) the mask is filled with stars (`*`). If there is too many elements in array passed into the run-time mask, the redundant elements are ommited. Should I keep this behavior or also throw an exception for too small and/or to great number of elements? So require always 9 elements or always lesser or equal number of elements than 9.

---

## Comment 49

> Username: barendgehrels  
> Created_at: 2015-05-13 20:02:01 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30269722  

Good point.  
  
I think \* is a very reasonable default, but I also would find it clear if there were no defaults... It's a de-9-im matrix, and it might be an unnoticed error if users fill it e.g. with 8 values instead of the supposed 9...  
If so (no defaults), for runtime I think an exception is then appropriate indeed. Especially too much is indeed always a kind of error-situation.  
  
Curious to other opinions.

---

## Comment 50

> Username: awulkiew  
> Created_at: 2015-05-16 01:32:24 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30457053  

Well, the last character corresponds to overlapping of Exterior/Exterior which will always be set to 2 for any 2d geometries combination (in general probably to the dimension of the input) so typically noone would check that. But I get your point.  
  
An alternative to the exception could be a run-time assertion (for static_mask a compile-time assertion). I guess such input might be considered as a programming error.  
  
Though indeed, in general an exception is thrown in such cases, e.g. std::invalid_argument in the STL.

---

## Comment 51

> Username: awulkiew  
> Created_at: 2015-05-18 17:36:47 UTC  
> Updated_at: 2015-05-26 15:27:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/288#discussion_r30529438  

I added checks for both types of mask. `static_mask` is checked using `BOOST_MPL_ASSERT_MSG`, `mask` ctors may throw a newly added `bg::invalid_input_exception`.  
  
I decided to not remove the possibility to pass a shorter or longer string. It's because similar semantics is also allowed in `std::bitset`. If a smaller number of bits is passed the remaining ones are filled with zeros. It's also possible to pass a string containing more characters than the number of bits in `std::bitmap`. It's even possible to pass a starting position and the number of characters/bits which are used to fill the object, though this last option is not implemented for `de9im::mask` right now.

---
