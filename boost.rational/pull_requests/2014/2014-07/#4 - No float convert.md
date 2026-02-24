# #4 No float convert [Closed]

> Username: jzmaddock  
> Created at: 2014-07-28 12:01:15 UTC  
> Updated at: 2016-12-28 19:27:36 UTC  
> Closed at: 2016-12-28 19:27:35 UTC  
> Url: https://github.com/boostorg/rational/pull/4  

This change disallows conversion from floating-point types - which brings the library into line with it's documentation.  Currently:  
  
rational<int> r(0.1);  
  
compiles just fine, but doesn't generate any kind of sensible answer.  See discussion: http://lists.boost.org/Archives/boost/2014/07/215504.php  
  
Note that this change includes (and depends upon) two previous pull requests:  
https://github.com/boostorg/rational/pull/2  
https://github.com/boostorg/rational/pull/3  
  
The only breaking change I can detect with this commit is a currently untested one:  
  
constexpr rational<short> r(3); // fails  
  
this works however:  
  
constexpr rational<short> r(static_cast<short>(3)); // OK  
  
While this is might be fixable, frankly I don't see the point - I suspect that real world usage of rational with shorter-than-int-types is vanishlingly small?

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-08-28 10:06:09 UTC  
> Url: https://github.com/boostorg/rational/pull/4#issuecomment-53699748  

Update: the changes above are incomplete and unfortunately break existing code (Boost.Multiprecision for example).  The issue is the inconsistency between the new constructors and the old-style overloaded operators.  The latter also have some gaping holes in them for example:  
  
rational<int> r(3);  
assert(r == 3.5); // Compiles and passes!!  
  
I'll post a more complete fix shortly.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2014-08-29 12:27:09 UTC  
> Url: https://github.com/boostorg/rational/pull/4#issuecomment-53868804  

Update: I've updated the proposed changes so that the non-member functions can no longer "accidentally" accept floating point values as arguments.  
  
Also tested with all the other Boost libraries that use Boost.Rational: there is one new failure in Geometry which apparently relies on an (unsafe) implicit conversion from double to rational<int> and therefore looks like a bug.  I'll file a separate comment on that.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2014-08-29 12:43:01 UTC  
> Url: https://github.com/boostorg/rational/pull/4#issuecomment-53870176  

The Geometry issue is here: https://github.com/boostorg/geometry/commit/c992eb61e8ef9e90fdae403ac6ff1eea12297291

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2014-10-18 15:44:14 UTC  
> Url: https://github.com/boostorg/rational/pull/4#issuecomment-59619042  

Update: the folks from Boost.Geometry say it's OK to go ahead.

---

## Comment 5

> Username: pdimov  
> Created_at: 2015-03-30 16:13:45 UTC  
> Url: https://github.com/boostorg/rational/pull/4#issuecomment-87738286  

Looks like that, after this change, rational would no longer need to depend on operators, which seems a good thing in itself. :-)

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2015-03-30 16:56:13 UTC  
> Url: https://github.com/boostorg/rational/pull/4#issuecomment-87749622  

> Looks like that, after this change, rational would no longer need to   
> depend on operators, which seems a good thing in itself. :-)  
  
Correct, the alternative to this patch would be to make rather   
complicated changes to Boost.Operator as well, frankly it's easier just   
to have all the operators defined in one place here.  As it happens, it   
makes the code way easier to debug as well ;)

---

## Comment 7

> Username: swatanabe  
> Created_at: 2015-03-30 18:03:21 UTC  
> Updated_at: 2016-12-28 18:45:09 UTC  
> Url: https://github.com/boostorg/rational/pull/4#discussion_r27415581  

This forbids conversion from a smaller unsigned type to a larger signed type, which is safe.

---

## Comment 8

> Username: swatanabe  
> Created_at: 2015-03-30 18:06:39 UTC  
> Updated_at: 2016-12-28 18:45:09 UTC  
> Url: https://github.com/boostorg/rational/pull/4#discussion_r27415895  

What about <= and >=?

---

## Comment 9

> Username: swatanabe  
> Created_at: 2015-03-30 18:08:40 UTC  
> Updated_at: 2016-12-28 18:45:09 UTC  
> Url: https://github.com/boostorg/rational/pull/4#discussion_r27416074  

Boost.Rational doesn't check for overflow anywhere else.  I don't think that this should throw.

---

## Comment 10

> Username: swatanabe  
> Created_at: 2015-03-30 18:16:20 UTC  
> Updated_at: 2016-12-28 18:45:09 UTC  
> Url: https://github.com/boostorg/rational/pull/4#discussion_r27416795  

This can be ambiguous with is_compatible_integer if T and IntType are class types with an implicit narrowing conversion.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2015-03-30 18:19:52 UTC  
> Updated_at: 2016-12-28 18:45:09 UTC  
> Url: https://github.com/boostorg/rational/pull/4#discussion_r27417144  

> Boost.Rational doesn't check for overflow anywhere else. I don't think   
> that this should throw.  
  
So would you permit assignment of -1 to a rational<unsigned> ?  
  
Bottom line, these conversions are inherently unsafe, and almost   
certainly do not do what the user wants/expects.  What harm do you see   
from more robust error handling?

---

## Comment 12

> Username: swatanabe  
> Created_at: 2015-03-30 18:24:10 UTC  
> Updated_at: 2016-12-28 18:45:09 UTC  
> Url: https://github.com/boostorg/rational/pull/4#discussion_r27417590  

throwing an exception implies that the exception can be caught.  If you're going to allow this, then it needs to be done less haphazardly.

---

## Comment 13

> Username: pdimov  
> Created_at: 2015-03-30 18:37:01 UTC  
> Updated_at: 2016-12-28 18:45:09 UTC  
> Url: https://github.com/boostorg/rational/pull/4#discussion_r27418841  

I don't see a better alternative. Disallowing construction from int altogether is too limiting as it would reject constants. If we go with an assert this makes the user do the work necessary to ensure that the assertion won't fire, at each assignment, instead of us doing the check once in rational. Throwing makes sense here.

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2015-03-30 18:40:09 UTC  
> Updated_at: 2016-12-28 18:45:09 UTC  
> Url: https://github.com/boostorg/rational/pull/4#discussion_r27419123  

> throwing an exception implies that the exception can be caught. If   
> you're going to allow this, then it needs to be done less haphazardly.  
  
I'm sorry, I still don't follow.  All potentially unsafe   
construction/assignment gets checked.  What else did you have in mind?

---

## Comment 15

> Username: swatanabe  
> Created_at: 2015-03-31 03:14:55 UTC  
> Updated_at: 2016-12-28 18:45:09 UTC  
> Url: https://github.com/boostorg/rational/pull/4#discussion_r27450309  

AMDG  
  
On 03/30/2015 12:37 PM, Peter Dimov wrote:  
  
> > -    template <class T>  
> > -    typename enable_if_c<...>::type assign(const T& n, const T& d)  
> > -    {  
> > -       if(!is_safe_narrowing_conversion(n) || !is_safe_narrowing_conversion(d))  
> > -          BOOST_THROW_EXCEPTION(bad_rational());  
>   
> I don't see a better alternative. Disallowing construction from int altogether is too limiting as it would reject constants. If we go with an assert this makes the user do the work necessary to ensure that the assertion won't fire, at each assignment, instead of us doing the check once in rational. Throwing makes sense here.  
  
If this reasoning applies here, then shouldn't it  
also apply to  
template < typename NewType >  
    BOOST_CONSTEXPR explicit  
    rational( rational<NewType> const &r );  
  
And what about operator +-*/?  Are we going to  
make those throw also?  
  
In Christ,  
Steven Watanabe

---

## Comment 16

> Username: jzmaddock  
> Created_at: 2015-03-31 08:25:15 UTC  
> Updated_at: 2016-12-28 18:45:09 UTC  
> Url: https://github.com/boostorg/rational/pull/4#discussion_r27460427  

> This forbids conversion from a smaller unsigned type to a larger   
> signed type, which is safe.  
>   
> Fixed.  Also fixed some overload ambiguities at the same time.

---

## Comment 17

> Username: jzmaddock  
> Created_at: 2015-03-31 08:29:05 UTC  
> Updated_at: 2016-12-28 18:45:09 UTC  
> Url: https://github.com/boostorg/rational/pull/4#discussion_r27460687  

> What about <= and >=?  
  
They're defined later as non-members.

---

## Comment 18

> Username: jzmaddock  
> Created_at: 2015-03-31 08:30:20 UTC  
> Updated_at: 2016-12-28 18:45:09 UTC  
> Url: https://github.com/boostorg/rational/pull/4#discussion_r27460757  

> This can be ambiguous with is_compatible_integer if T and IntType are   
> class types with an implicit narrowing conversion.  
>   
> Nod.  I believe my changes for signedness conversions fix this also.

---

## Comment 19

> Username: pdimov  
> Created_at: 2015-03-31 12:21:08 UTC  
> Updated_at: 2016-12-28 18:45:09 UTC  
> Url: https://github.com/boostorg/rational/pull/4#discussion_r27473476  

I agree that the same reasoning applies. The only two sensible behaviors I see for op+-*/ on overflow are to either throw, or return a representable approximation of the real result. (For op- with an underlying unsigned type, throwing when the result would be negative looks like the only option unless we just clamp at zero.)  
  
This does present a bit of a problem with op++/-- though, because an overflow/underflow check there would be measurable, so it should probably be skipped. The special case of +- with the same denominator would also be slowed down by the check.  
  
I agree that this is not a trivial decision.

---

## Comment 20

> Username: jzmaddock  
> Created_at: 2015-03-31 16:18:59 UTC  
> Updated_at: 2016-12-28 18:45:09 UTC  
> Url: https://github.com/boostorg/rational/pull/4#discussion_r27495182  

> I agree that the same reasoning applies. The only two sensible   
> behaviors I see for op+-*/ on overflow are to either throw, or return   
> the closest representable. (For op- with an underlying unsigned type,   
> throwing when the result would be negative looks like the only option.)  
>   
> This does present a bit of a problem with op++/-- though, because an   
> overflow/underflow check there would be measurable, so it should   
> probably be skipped. The special case of +- with the same denominator   
> would also be slowed down by the check.  
>   
> I agree that this is not a trivial decision.  
  
I think we're mixing two different issues here - the point of this patch   
is close some loopholes whereby narrowing conversions - in particular   
truncating-float-to-rational were previously allowed _by accident_.  In   
closing that one, I had to decide what to do about narrowing integer   
conversions as well.  These were also allowed by accident, not design,   
but I felt that there was too much potential to break old code by   
completely disallowing them.  Hence the runtime checks.  
  
Checking in the operators is a whole other ball game:  
- It's non-trivial (in comparison to the constructors), such checks   
  would require integer division if implemented portably, and in the   
  multiprecision case, that's ruinously expensive.  
- It would be darn hard to implement correctly - we would need a checked   
  version of lcm at the very least.  
- It's outside the scope of this patch, and frankly there are other   
  pieces of low hanging fruit that should be fixed first (non-throwing   
  move construct/assign would get my vote).  
- Now that we have Boost .Multiprecision there are other options -   
  unbounded integer types, or bounded and checked multiprecision ones. The   
  latter can check for overflow at virtually no cost, certainly much   
  cheaper than portable checking code in the operators could ever do.  
  
However, Steven is correct that I missed one other converting   
constructor: I've just added runtime checks to that as well for   
consistency.  I'll also add some compile-fail tests shortly to verify   
that stuff that should no longer compiler really doesn't.

---

## Comment 21

> Username: eldiener  
> Created_at: 2016-12-24 17:11:51 UTC  
> Url: https://github.com/boostorg/rational/pull/4#issuecomment-269092389  

I would like to push through this pull request, but changes to rational.hpp has created conflicts. Can this be updated John so that I can test it and then merge the pull request. I realize this has been ignored for a year and a half.

---

## Comment 22

> Username: jzmaddock  
> Created_at: 2016-12-24 18:36:52 UTC  
> Url: https://github.com/boostorg/rational/pull/4#issuecomment-269095530  

On 24/12/2016 17:11, Edward Diener wrote:  
>  
> I would like to push through this pull request, but changes to   
> rational.hpp has created conflicts. Can this be updated John so that I   
> can test it and then merge the pull request. I realize this has been   
> ignored for a year and a half.  
>  
  
Give me a few days and I'll take a look at the conflicts.  
  
John.

---

## Comment 23

> Username: jzmaddock  
> Created_at: 2016-12-28 19:27:35 UTC  
> Url: https://github.com/boostorg/rational/pull/4#issuecomment-269527202  

I'm going to submit a fresh PR, for some reason trying to fix this one didn't work :(

---
