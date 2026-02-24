# #125 - Questions about how to use multiprecision library [Closed]

> Username: robertramey  
> Created at: 2019-03-25 16:22:40 UTC  
> Updated at: 2021-01-17 17:49:57 UTC  
> Closed at: 2021-01-17 17:49:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/125  

I'm looking through the boost multi precision library documentation and I have a  few observations and questions.  I could probably dive into he source code and maybe find answers to my questions, but I don't think that should be necessary.  Note that it's entirely possible that I've overlooked some part of the documentation so feel free to point that out if I do.  
  
a) The is a very extensive package.  I'm very impressed.  I wish I had spent more time studying it before.  
  
b) the reference contains the line to document the + operator:  
  
unmentionable-expression-template-type operator+(const see-below&, const see-below&);  
  
I presume that "see-below" refers to an instance of the template "number<...>" though I can't find that explicitly.  unmentionable-expression-template-type I see could be an expression template of some sort or perhaps an instance of the template "number<...>" depending wether the expression template parameter has been specified.  Is this correct?  
  
1) Do the left/right arguments have to be the same type? or can the be of different types?  I searched the include files and don't find this function.  It seems that one depends upon the operator+= versions but I don't know. The following questions presume that the + operator (member or free) permits arguments to be of different types.  
  
2) What is the return type it expression templates are not used?  
  
3) are arguments promoted to be the same type?  If so what rules are used?   
  
As can be seen I'm still flaying around with this to understand this package.  FYI my interest is to create a case study for the safe numerics library.  The safe numerics library "should" work with any type which fulfills a definition of "numeric" and/or "integer" concept which I believe "number" does in fact fullfill.  So multi-precision datatypes should be able to use safe<multiprecision::number<...>> and it should "just work".  But since I haven't tested this yet, I probably need to tweak something over there.  
  
4) I notice that chris has created a "wide integer" library.  What is the motivation for that given the amount of effort invested in multi-precision.  
  
5) Looking at this, especially the "checked" facility, makes me wonder if safe numerics shouldn't have been part of multi-precision in the first place.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-03-25 18:47:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/125#issuecomment-476330004  

>I presume that "see-below" refers to an instance of the template "number<...>" though I can't find that explicitly. unmentionable-expression-template-type I see could be an expression template of some sort or perhaps an instance of the template "number<...>" depending wether the expression template parameter has been specified. Is this correct?  
  
The "see below" refers to the extended description lower down on that page (https://www.boost.org/doc/libs/1_66_0/libs/multiprecision/doc/html/boost_multiprecision/ref/number.html#boost_multiprecision.ref.number.non_member_operators), which says:  
  
" These operators all take their usual arithmetic meanings.  
  
The arguments to these functions must contain at least one of the following:  
  
    A number.  
    An expression template type derived from number.  
    Any type for which number has an implicit constructor - for example a builtin arithmetic type.  
  
The return type of these operators is either:  
  
    An unmentionable-type expression template type when ExpressionTemplates is true.  
    Type number<Backend, et_off> when ExpressionTemplates is false.  
    Type bool if the operator is a comparison operator.  
  
Finally note that the second argument to the left and right shift operations must be a builtin integer type, and that the argument must be positive (negative arguments result in a std::runtime_error being thrown). "  
  
I guess it would be nice to link the "see below"s to the relevant sections, it might take a day when I'm feeling extra-ordinarily bored to hook all those up though :(  
  
>Do the left/right arguments have to be the same type? or can the be of different types? I searched the include files and don't find this function. It seems that one depends upon the operator+= versions but I don't know. The following questions presume that the + operator (member or free) permits arguments to be of different types.  
  
They can be different types.  The only requirement is that there is an unambiguous conversion route that leads to a single result type with lossless conversions from all the arguments to that type.  Mixed precision arithmetic gets it's own page: https://www.boost.org/doc/libs/1_66_0/libs/multiprecision/doc/html/boost_multiprecision/tut/mixed.html  
  
And yes the docs could be improved quite a bit there.  
  
>What is the return type it expression templates are not used?  
  
It's a `number<>`, ie the same as the argument types, this is mentioned in the extended description quoted above.  
  
>are arguments promoted to be the same type? If so what rules are used?  
  
Yes, with bloody complicated rules!  OK actually not that complicated, just hard to implement.  Essentially the rule is:  
  
* For each argument type, is there an implicit  conversion from every other argument type?  If yes, add to the list of candidate result types.  
* If there is exactly one candidate result type, then use it as the result.  
* If there are more than one candidate result types then the operator is ambiguous and is not considered for overload resolution.  
* If there are no candidate result types, then the operator is not considered for overload resolution.  
  
We've done some work to improve the error messages from the last two cases recently, and yes this could/should be better documented along with examples of the error messages so folks can more easier diagnose issues.  
  
>I notice that chris has created a "wide integer" library. What is the motivation for that given the amount of effort invested in multi-precision.  
  
Good question, I know Chris is mostly interested in micro-processor stuff, so I suspect it's ultra-lightweight, but perhaps Chris will chip in.  
  
>Looking at this, especially the "checked" facility, makes me wonder if safe numerics shouldn't have been part of multi-precision in the first place.  
  
I'm fairly sure I've mentioned it ;)  
  
I'm sure there is a need for a lightweight facility for builtin integers though.  
  
The multiprecision case is somewhat different, as the answer to something like _"is there an overflow"_ is inherently known to the addition/multiplication algorithms with absolutely no need to perform any up-front checking.  Indeed multiprecision division is so ruinously expensive (as was hardware division on early processors) that it should probably be considered an error to pass a multiprecision type through a generic overflow-checker.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2019-03-25 19:48:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/125#issuecomment-476350466  

In addition to John's comments...  
Boost.Multiprecision has another great feature that Johnbuilt into the library from the very beginning. Boost.Multirpecision  
offrers the ability to wrap custom user-defined number backends.For instance, I have wrapped (but never published) two otherbig float backends for use with Boost.Multirpecision. Thisincreases the flexibility of this library significantly howeverthis feature is not often discussed in the threads.  
  
On another note, if I were to start over, I would makefrom the beginning a stronger attempt to create acompiler-variation of Boost.Multiprecision, possibly withlimited or reduced backend support, that eliminates the bulkpulled in from I/O streaming, string-streams, allocators, etc.This could provide a more embedded-friendly versionof the library. We could attempt this now, but we have notdiscussed this in detail.  
  
Yes, there is additional work on a wide integer library. Its mainintention is to provide wide unsigned integers for embeddedreal-time microcontroller systems that lack sufficient memoryfor the full inclusion of Boost.Multiprecision support.I use this library for deeply embedded code requiring,for instance, uint512_t, etc.  
  
Best regards, Chris  
    On Monday, March 25, 2019, 7:47:50 PM GMT+1, jzmaddock <notifications@github.com> wrote:    
   
   
  
I presume that "see-below" refers to an instance of the template "number<...>" though I can't find that explicitly. unmentionable-expression-template-type I see could be an expression template of some sort or perhaps an instance of the template "number<...>" depending wether the expression template parameter has been specified. Is this correct?  
  
  
The "see below" refers to the extended description lower down on that page (https://www.boost.org/doc/libs/1_66_0/libs/multiprecision/doc/html/boost_multiprecision/ref/number.html#boost_multiprecision.ref.number.non_member_operators), which says:  
  
" These operators all take their usual arithmetic meanings.  
  
The arguments to these functions must contain at least one of the following:  
A number.  
An expression template type derived from number.  
Any type for which number has an implicit constructor - for example a builtin arithmetic type.  
  
The return type of these operators is either:  
An unmentionable-type expression template type when ExpressionTemplates is true.  
Type number<Backend, et_off> when ExpressionTemplates is false.  
Type bool if the operator is a comparison operator.  
  
Finally note that the second argument to the left and right shift operations must be a builtin integer type, and that the argument must be positive (negative arguments result in a std::runtime_error being thrown). "  
  
I guess it would be nice to link the "see below"s to the relevant sections, it might take a day when I'm feeling extra-ordinarily bored to hook all those up though :(  
  
  
Do the left/right arguments have to be the same type? or can the be of different types? I searched the include files and don't find this function. It seems that one depends upon the operator+= versions but I don't know. The following questions presume that the + operator (member or free) permits arguments to be of different types.  
  
  
They can be different types. The only requirement is that there is an unambiguous conversion route that leads to a single result type with lossless conversions from all the arguments to that type. Mixed precision arithmetic gets it's own page: https://www.boost.org/doc/libs/1_66_0/libs/multiprecision/doc/html/boost_multiprecision/tut/mixed.html  
  
And yes the docs could be improved quite a bit there.  
  
  
What is the return type it expression templates are not used?  
  
  
It's a number<>, ie the same as the argument types, this is mentioned in the extended description quoted above.  
  
  
are arguments promoted to be the same type? If so what rules are used?  
  
  
Yes, with bloody complicated rules! OK actually not that complicated, just hard to implement. Essentially the rule is:  
     
   - For each argument type, is there an implicit conversion from every other argument type? If yes, add to the list of candidate result types.  
   - If there is exactly one candidate result type, then use it as the result.  
   - If there are more than one candidate result types then the operator is ambiguous and is not considered for overload resolution.  
   - If there are no candidate result types, then the operator is not considered for overload resolution.  
  
We've done some work to improve the error messages from the last two cases recently, and yes this could/should be better documented along with examples of the error messages so folks can more easier diagnose issues.  
  
  
I notice that chris has created a "wide integer" library. What is the motivation for that given the amount of effort invested in multi-precision.  
  
  
Good question, I know Chris is mostly interested in micro-processor stuff, so I suspect it's ultra-lightweight, but perhaps Chris will chip in.  
  
  
Looking at this, especially the "checked" facility, makes me wonder if safe numerics shouldn't have been part of multi-precision in the first place.  
  
  
I'm fairly sure I've mentioned it ;)  
  
I'm sure there is a need for a lightweight facility for builtin integers though.  
  
The multiprecision case is somewhat different, as the answer to something like "is there an overflow" is inherently known to the addition/multiplication algorithms with absolutely no need to perform any up-front checking. Indeed multiprecision division is so ruinously expensive (as was hardware division on early processors) that it should probably be considered an error to pass a multiprecision type through a generic overflow-checker.  
  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-03-25 20:43:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/125#issuecomment-476369454  

>I have wrapped (but never published) two otherbig float backends for use with Boost.Multirpecision.  
  
I'd be curious to know what they were.  
  
>On another note, if I were to start over, I would makefrom the beginning a stronger attempt to create acompiler-variation of Boost.Multiprecision, possibly withlimited or reduced backend support, that eliminates the bulkpulled in from I/O streaming, string-streams, allocators, etc.This could provide a more embedded-friendly versionof the library. We could attempt this now, but we have notdiscussed this in detail.  
  
I think this is worth looking at, something I'm really keen to try again is to get a properly constexpr cpp_int, but it really need `std::is_constant_evaluated` which is coming but not quite here yet.  Fully constexpr big-integers could be a big deal in that environment if we could make some of the "bulk" conditional.  It's always a compromise between user requests for more features and um, bloat :(

---

## Comment 4

> Username: ckormanyos  
> Created at: 2019-03-27 19:12:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/125#issuecomment-477309005  

>> I have wrapped (but never published) two otherbig float backends  
  
> I'd be curious to know what they were.  
  
One is proprietary. The other backend that was wrapped is the original prototyping work that was actually the predecessor of e_float. This ancestral work began in 1999, so it's 20 years now. The project was called mp_cpp and it is still maintained. I will send a PM regarding mp_cpp. It is cool because it has even more algorithms that we could store away in the toolbox until we are ready to use them. There are some for iterative calculation of trig, exp, etc. It also uses FFTW for big multiplication. The library uses decimal float with known issues and has a few other buglets. But some ideas are nice within this acestral work. It was cool to wrap it in modern Boost.Multiprecision.

---

## Comment 5

> Username: ckormanyos  
> Created at: 2019-03-27 19:15:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/125#issuecomment-477310100  

> > eliminates the bulkpulled in from I/O streaming, string-streams, allocators, etc.  
  
> I think this is worth looking at, something I'm really keen to try again is to get a properly constexpr cpp_int  
  
These ideas seem like they could form a sensible basis for a potential topic for a potential GSoC project in the future...

---

## Comment 6

> Username: ckormanyos  
> Created at: 2021-01-17 17:49:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/125#issuecomment-761851319  

It seems like there is definitive action to be taken on this issue.  
  
Potential enhancements for the future include:  
  
- Continue progress on advanced multiplication schemes.  
- Use advanced language features as they become mainstream, see also #288   
- Possibility to deactivate at compile time certain heavy-weight features I/O streaming, stringstream, etc.
