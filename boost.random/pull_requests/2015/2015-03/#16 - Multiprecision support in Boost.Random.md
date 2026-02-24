# #16 Multiprecision support in Boost.Random [Closed]

> Username: jzmaddock  
> Created at: 2015-03-08 18:54:40 UTC  
> Updated at: 2015-03-14 18:10:30 UTC  
> Closed at: 2015-03-14 18:10:30 UTC  
> Url: https://github.com/boostorg/random/pull/16  

Steven, this is I hope the completed pull request.  Tested with MSVC 10, 11, 12, GCC-4.8, 4.9 in 03 and 11 modes.  Intel-15 (win and linux), clang/linux, and oracle/solaris.  
  
Thankfully very few changes were required for the floating-point distros.  I also got discrete_distribution working with both large ints, and multiprecision float weights (though I'm not sure what the point would be).  
  
And finally, a few headers were not being included by boost/random.hpp which is also fixed by this.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-03-08 19:09:23 UTC  
> Url: https://github.com/boostorg/random/pull/16#issuecomment-77768207  

Forgot to mention, you need multiprecision develop branch to build against this.  I also need to make changes to the multiprecision lib tests/headers once this goes in.

---

## Comment 2

> Username: swatanabe  
> Created_at: 2015-03-09 18:27:39 UTC  
> Url: https://github.com/boostorg/random/pull/16#issuecomment-77913304  

AMDG  
  
I'm getting warnings from msvc because random/detail/traits.hpp  
has #include enable_warnings.hpp without a corresponding  
disable_warnings.hpp  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2015-03-09 18:48:32 UTC  
> Url: https://github.com/boostorg/random/pull/16#issuecomment-77917233  

> I'm getting warnings from msvc because random/detail/traits.hpp  
> has #include enable_warnings.hpp without a corresponding  
> disable_warnings.hpp  
  
Oops, pushed the obvious fix.  
  
John.

---

## Comment 4

> Username: swatanabe  
> Created_at: 2015-03-09 19:08:13 UTC  
> Url: https://github.com/boostorg/random/pull/16#issuecomment-77920898  

AMDG  
  
On 03/08/2015 12:54 PM, jzmaddock wrote:  
  
> Steven, this is I hope the completed pull request.  Tested with MSVC 10, 11, 12, GCC-4.8, 4.9 in 03 and 11 modes.  Intel-15 (win and linux), clang/linux, and oracle/solaris.  
  
MSVC 9 and GCC 4.6 are also okay.  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: swatanabe  
> Created_at: 2015-03-09 23:51:47 UTC  
> Updated_at: 2015-03-14 11:47:48 UTC  
> Url: https://github.com/boostorg/random/pull/16#discussion_r26088267  

I'd prefer not to change the indentation here

---

## Comment 6

> Username: swatanabe  
> Created_at: 2015-03-09 23:57:01 UTC  
> Updated_at: 2015-03-14 11:47:48 UTC  
> Url: https://github.com/boostorg/random/pull/16#discussion_r26088566  

We can get away with reinterpret_cast for builtin signed/unsigned types.  Not so much for user-defined types.

---

## Comment 7

> Username: swatanabe  
> Created_at: 2015-03-10 00:03:46 UTC  
> Updated_at: 2015-03-14 11:47:48 UTC  
> Url: https://github.com/boostorg/random/pull/16#discussion_r26088928  

Should be traits.hpp.  Also, is this a public header or not?  If it's not public, it should not be included in the documentation and should not be visible to users.  If it is public, it shouldn't be under detail/.

---

## Comment 8

> Username: swatanabe  
> Created_at: 2015-03-10 00:25:48 UTC  
> Updated_at: 2015-03-14 11:47:48 UTC  
> Url: https://github.com/boostorg/random/pull/16#discussion_r26090118  

I'm somewhat curious why the expression templates don't support these conversions, when multiprecision::number itself does.  I do notice that detail::expression has an (explicit) conversion to bool.

---

## Comment 9

> Username: swatanabe  
> Created_at: 2015-03-10 00:37:29 UTC  
> Updated_at: 2015-03-14 11:47:48 UTC  
> Url: https://github.com/boostorg/random/pull/16#discussion_r26090837  

Is it absolutely necessary to change the format?  Have you at least checked that the original format is still readable by the new code.

---

## Comment 10

> Username: swatanabe  
> Created_at: 2015-03-10 02:06:57 UTC  
> Updated_at: 2015-03-14 11:47:48 UTC  
> Url: https://github.com/boostorg/random/pull/16#discussion_r26094318  

Why is this test run in release mode?

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2015-03-10 12:21:02 UTC  
> Updated_at: 2015-03-14 11:47:48 UTC  
> Url: https://github.com/boostorg/random/pull/16#discussion_r26116408  

Editor SNAFU, fix pushed.

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2015-03-10 13:23:50 UTC  
> Updated_at: 2015-03-14 11:47:48 UTC  
> Url: https://github.com/boostorg/random/pull/16#discussion_r26120391  

Over aggressive search and replace at fault: reverted that change and one other, and added static_asserts to validate preconditions.

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2015-03-10 13:34:28 UTC  
> Updated_at: 2015-03-14 11:47:48 UTC  
> Url: https://github.com/boostorg/random/pull/16#discussion_r26121189  

> In include/boost/random/detail/traits.hpp   
> https://github.com/boostorg/random/pull/16#discussion_r26088928:  
>   
> > @@ -0,0 +1,107 @@  
> > +/\* boost random/detail/seed.hpp header file  
>   
> Should be traits.hpp. Also, is this a public header or not? If it's   
> not public, it should not be included in the documentation and should   
> not be visible to users. If it is public, it shouldn't be under detail/.  
>   
> OK, moved to boost/random.  This is pretty much a "documented detail", I   
> wouldn't expect anyone to need to specialize these traits, but you never   
> know.  Better documented than not IMO.

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2015-03-10 17:10:22 UTC  
> Updated_at: 2015-03-14 11:47:48 UTC  
> Url: https://github.com/boostorg/random/pull/16#discussion_r26142120  

> I'm somewhat curious why the expression templates don't support these   
> conversions, when multiprecision::number itself does. I do notice that   
> detail::expression has an (explicit) conversion to bool.  
>   
> Good question, it got overlooked I guess, conversions between number<>   
> types are handled via converting constructors it's just conversions to   
> other types (built in ones for example) that's missing.  I realise this   
> would simply those horrible double-casts, let me experiment and see if I   
> can add support for this to multiprecision without breaking anything.

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2015-03-10 17:12:43 UTC  
> Updated_at: 2015-03-14 11:47:48 UTC  
> Url: https://github.com/boostorg/random/pull/16#discussion_r26142392  

> Why is this test run in release mode?  
  
It's too darn slow in debug mode - those calls to seed() in the   
lagged_fibonacci sequences are the culprit - the lack of inlining really   
hurts in there (and they generate a _lot_ of values).  Will add a comment.

---

## Comment 16

> Username: jzmaddock  
> Created_at: 2015-03-10 17:43:01 UTC  
> Updated_at: 2015-03-14 11:47:48 UTC  
> Url: https://github.com/boostorg/random/pull/16#discussion_r26145777  

> Is it absolutely necessary to change the format? Have you at least   
> checked that the original format is still readable by the new code.  
  
I realise this is a horrible fix.  It's not going to be easy to fix   
upstream in multiprecision, but let me look at it again and see.

---

## Comment 17

> Username: jzmaddock  
> Created_at: 2015-03-11 19:30:22 UTC  
> Updated_at: 2015-03-14 11:47:48 UTC  
> Url: https://github.com/boostorg/random/pull/16#discussion_r26246810  

> Is it absolutely necessary to change the format? Have you at least   
> checked that the original format is still readable by the new code.  
  
Reverted, and fixed upstream in multiprecision.  
  
John.

---

## Comment 18

> Username: jzmaddock  
> Created_at: 2015-03-12 18:00:15 UTC  
> Updated_at: 2015-03-14 11:47:48 UTC  
> Url: https://github.com/boostorg/random/pull/16#discussion_r26327569  

Fixed upstream in multiprecision and reverted this change: unfortunately adding explicit conversion operators to the expression templates caused new issues in chi_squared_distribution.hpp which required the "obvious" casts.  You win some you lose some I guess... whatever adding the explicit conversion operators to the expression templates is probably the right thing to do anyway.  
  
I think that's all the points you raised addressed now?

---

## Comment 19

> Username: swatanabe  
> Created_at: 2015-03-12 19:07:04 UTC  
> Updated_at: 2015-03-14 11:47:48 UTC  
> Url: https://github.com/boostorg/random/pull/16#discussion_r26334159  

AMDG  
  
On 03/12/2015 12:00 PM, jzmaddock wrote:  
  
> I think that's all the points you raised addressed now?  
  
I think so.  I'll try to review it again  
by the end of this week.  
  
In Christ,  
Steven Watanabe

---

## Comment 20

> Username: jzmaddock  
> Created_at: 2015-03-12 19:17:38 UTC  
> Updated_at: 2015-03-14 11:47:48 UTC  
> Url: https://github.com/boostorg/random/pull/16#discussion_r26335017  

> On 03/12/2015 12:00 PM, jzmaddock wrote: I think that's all the points   
> you raised addressed now?  
> I think so. I'll try to review it again by the end of this week. In   
> Christ, Steven Watanabe  
  
Thanks, no rush, as it's too late for this release anyway, John.

---

## Comment 21

> Username: swatanabe  
> Created_at: 2015-03-13 21:22:57 UTC  
> Updated_at: 2015-03-14 11:47:48 UTC  
> Url: https://github.com/boostorg/random/pull/16#discussion_r26424112  

AMDG  
  
On 03/12/2015 12:00 PM, jzmaddock wrote:  
  
> > @@ -60,7 +60,7 @@ class uniform_int_float  
> >      result_type operator()()  
> >      {  
> >          base_result range = static_cast<base_result>((max)())+1;  
> > -        return static_cast<result_type>(_rng() \* range);  
> > -        return static_cast<result_type>(static_cast<base_result>(_rng() \* range));  
>   
> Fixed upstream in multiprecision and reverted this change: unfortunately adding explicit conversion operators to the expression templates caused new issues in chi_squared_distribution.hpp which required the "obvious" casts.  You win some you lose some I guess... whatever adding the explicit conversion operators to the expression templates is probably the right thing to do anyway.  
  
Did you push this update?  I don't see it on my end.  
  
Also:  
- There are several places that change to 3 space indent,  
  when the surrounding code is all indented to 4 spaces.  
- traits.hpp has a _DETAIL_ #include guard.  
- doc/Jamfile.v2 refers to detail/traits.hpp  
- In independent_bits_engine, I'd prefer all the  
  private functions to be at the end.  base_result_type  
  should be hidden from doxygen (and I'd also prefer  
  not to rename it).  
  
In Christ,  
Steven Watanabe

---

## Comment 22

> Username: jzmaddock  
> Created_at: 2015-03-14 11:55:37 UTC  
> Url: https://github.com/boostorg/random/pull/16#discussion_r26437336  

> Did you push this update? I don't see it on my end.  
  
My bad, now pushed.  
  
> There are several places that change to 3 space indent, when the   
> surrounding code is all indented to 4 spaces.  
  
Those should be fixed now.  
  
> traits.hpp has a _DETAIL_ #include guard.  
> doc/Jamfile.v2 refers to detail/traits.hpp  
  
Fixed.  
  
> In independent_bits_engine, I'd prefer all the private functions to be   
> at the end.  
  
Fixed.  
  
> base_result_type should be hidden from doxygen (and I'd also prefer   
> not to rename it).  
  
This one is trickier: base_result_type is used as the type used to seed   
by a single value: and since it just gets forwarded to the underlying   
generator, this is actually the correct type for a single valued seed.    
So the type _is_ used in the interface, and is named to match the other   
public typedefs.  The alternative would be to make the seed type the   
same as the result type and explicitly cast to the base_result_type   
internally.  However, this has two key disadvantages - it's C++11 only,   
and it's also rather inefficient (we construct a wider/multiprecision   
type, just to narrow it again internally).  Oh, and it's illogical too ;)  
  
BTW there are rather a lot of commits now, let me know if you want me to   
condense down to a single commit.  
  
John.

---

## Comment 23

> Username: swatanabe  
> Created_at: 2015-03-14 15:20:34 UTC  
> Url: https://github.com/boostorg/random/pull/16#discussion_r26438659  

AMDG  
  
On 03/14/2015 05:55 AM, jzmaddock wrote:  
  
> > base_result_type should be hidden from doxygen (and I'd also prefer   
> > not to rename it).  
>   
> This one is trickier: base_result_type is used as the type used to seed   
> by a single value: and since it just gets forwarded to the underlying   
> generator, this is actually the correct type for a single valued seed.    
> So the type _is_ used in the interface, and is named to match the other   
> public typedefs.  The alternative would be to make the seed type the   
> same as the result type and explicitly cast to the base_result_type   
> internally.  However, this has two key disadvantages - it's C++11 only,   
> and it's also rather inefficient (we construct a wider/multiprecision   
> type, just to narrow it again internally).  Oh, and it's illogical too ;)  
  
Okay.  I don't like it, but there's no good solution.  
  
> BTW there are rather a lot of commits now, let me know if you want me to   
> condense down to a single commit.  
  
That would be helpful.  A few commits should be  
separate, though.  For example, adding missing  
#includes to random.hpp.  
  
In Christ,  
Steven Watanabe

---

## Comment 24

> Username: jzmaddock  
> Created_at: 2015-03-14 18:10:30 UTC  
> Url: https://github.com/boostorg/random/pull/16#issuecomment-80650078  

Closing - please see other "rationalised" PR.

---
