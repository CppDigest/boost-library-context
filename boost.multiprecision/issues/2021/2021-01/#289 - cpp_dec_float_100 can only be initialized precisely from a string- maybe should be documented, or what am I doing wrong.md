# #289 - cpp_dec_float_100 can only be initialized precisely from a string? maybe should be documented, or what am I doing wrong? [Open]

> Username: d3fault  
> Created at: 2021-01-15 01:31:53 UTC  
> Updated at: 2021-02-26 10:54:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289  

Just started using Boost.Multiprecision and struggled to get a cpp_dec_float_100 initialized to some hardcoded value. Eventually got it working once I parsed a string containing that hardcoded value. The docs didn't mention this I just sort of got lucky and deduced it.  
  
Should this be documented?  
Is there a better way to do it lol?  
  
```  
#include <iostream>  
#include "boost/multiprecision/cpp_dec_float.hpp"  
  
int main(int argc, char *argv[])  
{  
    using namespace std;  
    using namespace boost::multiprecision;  
    using EnormousFloat = cpp_dec_float_100;  
    EnormousFloat i = 6942666.123456789098765432123456789098765432199999999999999999999999999999999999999999999999999999999999999999;  
    cout << setprecision(numeric_limits<EnormousFloat>::max_digits10) << i << endl;  
    //outputs: 6942666.12345678918063640594482421875  
  
    string buffer("6942666.123456789098765432123456789098765432199999999999999999999999999999999999999999999999999999999999999999");  
    istringstream is(buffer);  
    EnormousFloat parsed;  
    is >> parsed;  
    cout << setprecision(numeric_limits<EnormousFloat>::max_digits10) << parsed << endl;  
    //outputs: the actual full value as above/desired  
  
    return 0;  
}  
```

---

## Comment 1

> Username: ckormanyos  
> Created at: 2021-01-15 05:54:38 UTC  
> Updated at: 2021-01-15 05:57:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-760664212  

> Just started using Boost.Multiprecision and struggled to get a `cpp_dec_float_100` initialized to some hardcoded value.  
  
Your observation is correct and this is, in fact, the correct behavior.  
  
`EnormousFloat` has many desired decimal digits of precision. There is no built-in type having so many digits. Thus the string representation is required in order to express (or convert) this desired rational number to an approximation using `cpp_dec_float_100`  
  
Consider the examples below.  
  
```  
#include <iomanip>  
#include <iostream>  
#include <numbers>  
  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
using big_float_type = boost::multiprecision::cpp_dec_float_100;  
  
int main()  
{  
  // Exact.  
  big_float_type twelve = 12U;  
  
  std::cout << std::setprecision(std::numeric_limits<big_float_type>::digits10)  << twelve << std::endl;  
  
  // Also exact because it is 1/2 + 1/8  
  big_float_type five_eighths = 0.625F;  
  
  std::cout << std::setprecision(std::numeric_limits<big_float_type>::digits10)  << five_eighths << std::endl;  
  
  // Lots of digits of 1/3  
  big_float_type big_third = big_float_type(1) / 3;  
  
  std::cout << std::setprecision(std::numeric_limits<big_float_type>::digits10)  << big_third << std::endl;  
  
  // A piece of pi N[Pi, 105]  
  big_float_type big_pi = big_float_type("3.14159265358979323846264338327950288419716939937510582097494459230781640628620899862803482534211706798215");  
  
  std::cout << std::setprecision(std::numeric_limits<big_float_type>::digits10)  << big_pi << std::endl;  
}  
```  
  
Here we find that integers like 12 can be represented exactly. Also fractions of liniear combinations of small `2^-n` are exact. The same, by the way, holds true for `float` or `double`. if, however, we really want lots of digits of 1/3 or pi, we need to ensure that these are properly initialized with a rational division or an actual string, whatever works in each case.  
  
> Should this be documented?  
  
I believe it is, though let's find out if the docs are clear and overt enough on this matter.  
  
> Is there a better way to do it lol?  
  
We think it is _best_ the way it is. You could, if you wanted, limit representation to the precision of built-in `float`, `double` or even `long double`. But this would not reflect the actual binary representation of these numbers. So at the end of the day, if you need a hunderd digits of pi, you must write them, or similar for other approximations...

---

## Comment 2

> Username: d3fault  
> Created at: 2021-01-15 06:50:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-760692097  

oh I didn't realize cpp_dec_float_100 had a string constructor (I skimmed docs, never saw it. I'm sure it's there but not obvious).  
  
my knee-jerk reaction is to say that since assigning from a non-string *can* lose precision that those constructor overloads/assignments should be deleted.... but then that would probably make the class pretty useless since now you can't do:  
```  
double x = 3.14;  
cpp_dec_float_100 y = x;  
```  
which seems like a perfectly normal use.  
  
if only there was a way to emit a warning when the user was initializing via *hardcoded* literal double, like so:  
`cpp_dec_float_100 x(3.14159265358979323846264338327950288419716939937510582097494459230781640628620899862803482534211706798215); //WARNING: potential loss of precision detected, wrap literal in quotes and you're golden`  
  
I mean I get that the compiler is interpreting it as a double (at least that's my best guess as to what's happening), but it is still a gotcha to newcomers (I been working with Multiprecision for less than a day and it bit me for a decent chunk of time)

---

## Comment 3

> Username: pabristow  
> Created at: 2021-01-15 09:34:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-760782009  

This is a massive pit waiting for the unwary to drop into.  There are numerous warning in the docs and example, but obviously never enough.  
  
And it will still bite you at some time in the future - been there, been bitten :-( .  
  
I have long wondered if a macro-controlled option might be feasible to warn about all uses of implicit conversion from double or float, at least in the constructor?  So when people get unexpected results, they can switch this on as a debugging aid.  
  
The dilemma is that it is very inconvenient to write code if there is not an implicit constructor from double.

---

## Comment 4

> Username: d3fault  
> Created at: 2021-01-15 23:37:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-761255556  

libclang (a la clang-tidy) can detect and warn when initializing via a  
hardcoded double literal... but I'm not sure if there's a way to  
detect it during normal compilation  
  
On 1/15/21, Paul A. Bristow <notifications@github.com> wrote:  
> This is a massive pit waiting for the unwary to drop into.  There are  
> numerous warning in the docs and example, but obviously never enough.  
>  
> And it will still bite you at some time in the future - been there, been  
> bitten :-( .  
>  
> I have long wondered if a macro-controlled option might be feasible to warn  
> about all uses of implicit conversion from double or float, at least in the  
> constructor?  So when people get unexpected results, they can switch this on  
> as a debugging aid.  
>  
> The dilemma is that it is very inconvenient to write code if there is not an  
> implicit constructor from double.  
>  
> --  
> You are receiving this because you authored the thread.  
> Reply to this email directly or view it on GitHub:  
> https://github.com/boostorg/multiprecision/issues/289#issuecomment-760782009

---

## Comment 5

> Username: ckormanyos  
> Created at: 2021-01-17 17:52:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-761851739  

It seems like there is no direct action needed for this ticket.  
  
Potential enhancements include:  
- Issue compile-time warnings for truncated-precision initialization where possible.  
- If right place and passages can be found, add even more clarity in docs regarding initialization (expecially from `double`, etc.) vs. precision of the big number type.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2021-01-17 18:12:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-761855050  

Sorry for being late to the party: I fear I'm with Chris on this one, there's nothing much we can actually do here without causing massive breakages (by banning all such conversions).  
  
In an ideal world we would:  
  
* Detect when initializing from a floating point literal (which requires a change to the language standard) and  
* Check whether the value looks exact or not - but there's no way we can tell for sure, since 0.5000000000000000000001 would "look like" and exact 0.5 once it's truncated to double :(

---

## Comment 7

> Username: d3fault  
> Created at: 2021-01-18 20:51:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-762465451  

I see no mention of this gotcha on this page of documentation: [cpp_dec_float](https://www.boost.org/doc/libs/1_75_0/libs/multiprecision/doc/html/boost_multiprecision/tut/floats/cpp_dec_float.html). And indeed the example at the bottom of that page encourages the "from numeric literal" approach of initialization.  
  
I dove straight into cpp_dec_float in a "get something working as fast as possible" mentality...  
  
So there is still an action to be taken: document it (better). Replace all uses of initialization in examples/docs to use the string versions... so that newcomers are less likely to get bitten.

---

## Comment 8

> Username: d3fault  
> Created at: 2021-01-18 21:03:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-762469449  

I just skimmed the [Introduction](https://www.boost.org/doc/libs/1_75_0/libs/multiprecision/doc/html/boost_multiprecision/intro.html) doc and *still* saw no mention of this gotcha. I've never seen it mentioned anywhere except this bug report.

---

## Comment 9

> Username: ckormanyos  
> Created at: 2021-01-18 21:50:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-762485361  

> ... saw no mention of this gotcha...  
  
Maybe we (or I) closed this issue too soon. Or maybe we should write a passage about initializing multiprecision float types in their constructors.  
  
I made a little graphic with some red arrows showing where such a section on _Initializing Float Types_ or similar might make sense. (See image below.)  
  
Thoughts?  
  
![grafik](https://user-images.githubusercontent.com/2404721/104965686-8b06f480-59df-11eb-8508-b87c62c77eb3.png)

---

## Comment 10

> Username: ckormanyos  
> Created at: 2021-02-23 22:06:09 UTC  
> Updated at: 2021-02-23 22:07:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-784546573  

> passage about initializing multiprecision float types in their constructors.  
  
I would like to move forward on this issue, but we need a decision to either close it with no Boost changes (but increased awareness) or to make a small addition to the docs along the lines above or similar.  
  
Thoughts?

---

## Comment 11

> Username: ckormanyos  
> Created at: 2021-02-24 06:51:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-784836520  

If we do decide to add a few lines to the docs, I will be happy to draft or write them.

---

## Comment 12

> Username: jzmaddock  
> Created at: 2021-02-24 10:43:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-784984473  

Adding an example with suitably eye catching title sounds good to me!

---

## Comment 13

> Username: ckormanyos  
> Created at: 2021-02-24 19:13:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-785309844  

> Adding an example with suitably eye catching title  
  
Agrred. See #301.

---

## Comment 14

> Username: d3fault  
> Created at: 2021-02-25 07:39:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-785689208  

That's definitely progress, but I still recommend updating all the existing docs/examples to use the string constructor.

---

## Comment 15

> Username: ckormanyos  
> Created at: 2021-02-25 10:44:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-785800806  

> recommend updating all the existing docs/examples to use the string constructor.  
  
I feel rather strongly like it is not a _one or the other_ decision. Developers are free to use construction or assignment from either string, built-in floating-point or integral typse, or from other multiprecision types. And exactly this situation is reflected in the documentation. So to answer your query, I do not see the need to pursue that idea you propose.

---

## Comment 16

> Username: pabristow  
> Created at: 2021-02-25 12:46:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-785869961  

I note that users (even expert C++ers, and not just me) keep falling into this big pit and quietly getting reduction to double. precision.  This suggests that we can't have too many warnings about the risk.  The results are very quiet and very hard to pinpoint.  Increasing use of auto increases the risk further.  
  
I'd also still like the ability to temporarily 'switch off' the constructors from double etc, probably using a macro that users may defines to do this.  
  
Any compile errors that this provokes will highlight potential causes of loss of precision.

---

## Comment 17

> Username: ckormanyos  
> Created at: 2021-02-25 13:33:11 UTC  
> Updated at: 2021-02-25 13:35:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-785897872  

> the ability to temporarily 'switch off' the constructors from double etc, probably using a macro  
  
I have done things like this in embedded micdrocontroller projects where developers might really not tolerate heavyweight components such as IO-streaming. I could imagine doing something like this here might increase confusion rather than decrease it?  
  
We could isolate the _perfect and pure_ ctors and assignments of string(s) and integrals?  
  
Maybe @jzmaddock will weigh in, although there is really a lot going on now.  
  
My first reaction is that if we do this, then maybe in another issue?

---

## Comment 18

> Username: ckormanyos  
> Created at: 2021-02-25 13:37:44 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-785900514  

If we do end up pursuing this, I could imagine there might be a lot of edits needed in the Math library as well.

---

## Comment 19

> Username: jzmaddock  
> Created at: 2021-02-25 18:48:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-786122158  

The more I think about this, the less I like any further action.  
  
Consider:  
  
```  
long double big_pi = 3.14159265358979323846264338327950288419716939937510;  // Oops!!  
```  
  
Which is *exactly* the same trap that the OP fell into, and is clearly nothing to do with us.  No compiler that I know of will warn on this either.  
  
Now consider making things stricter, if we make construction from floating point types explicit then:  
  
* We certainly break usage with Boost.Math, this could be fixable (but see below).  
* We break usage with almost any other generic mathematical code too, this could be at best very painful, at worst impossible to fix.  
* The fixes are unpleasant, consider this:  
  
```  
template <class T> T foo(T x)  
{  
  if(x > 0.1)  
  {  /* method 1 */ }  
  else  
  { /* method 2 */ }  
  return something;  
}  
```  
  
Code like this is utterly ubiquitous in Boost.Math, and numerical code in general, and relies on an implicit conversion from type `double` to whatever type T is used in this function.  Now, as far as Boost Math goes, we could add an explicit conversion from the 0.1 to T, but this precludes an important optimisation - we do not necessarily need to construct an actual temporary if T has the nous to perform the comparison directly - mpfr does for example, and avoids a memory allocation in the temporary that would otherwise be created (full disclosure - I haven't checked yet whether we're currently making use of this, but we certainly _should_ be and MPFR has the necessary functions).  
  
OK, so how about a warning?  Well first off there is no good portable mechanism to actually do this, but on a practical note, the pages and pages of warnings and backtraces you would get from Boost.Math usage say, would be so problematic that we would rightly get a lot of complaints.  The urge to "fix" things with some casts would be overwhelming, but as noted above, would actually make things worse!  
  
Something we do need to investigate more when we have time, is adding a) constexpr support for cpp_bin_float, and b) a reasonably generic mechanism for describing user-defined numeric literals, so to take Chris's example of 0.1 as a decimal string:  
  
```  
cpp_dec_float_50 d = 0.1_mpf;  
```  
  
Would do the right thing, as would:  
  
```  
cpp_bin_float_50 b = 0.1_mpf;  
```  
  
with the latter being inexact, and the former exact.  
  
And finally.... there is no "loss of precision" in converting from double to a multiprecision type.  None, not one bit.  In fact the conversion is _absolutely exact_ as long as the target type is binary.  In the OP's case the precision loss occurred prior to the library ever seeing the value as a result of the way the value was encoded as a double literal.  I feel it's important to stress this lest folks get the wrong idea that the library somehow loses digits when converting from double, because it absolutely does not do so.  
  
Whew, and apologies if this came across as a bit of rant :)

---

## Comment 20

> Username: ofloveandhate  
> Created at: 2021-02-25 19:23:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-786142801  

I find this conversation to be very valuable, in understanding when to construct multiprecision `T`'s, and when not to.

---

## Comment 21

> Username: ckormanyos  
> Created at: 2021-02-25 21:32:50 UTC  
> Updated at: 2021-02-25 21:33:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-786242153  

>  adding a) constexpr support for cpp_bin_float, and b) a reasonably generic mechanism for describing user-defined numeric literals  
  
Agreed on this and all your other points. User-defined literals with suffixes might be the way to go. I get the feeling, nonetheless, that handling this issue may become easier in upcoming years as more and more language features and implementation flexibility becomes available in Boost.Multiprecision --- a long-term perspective.  
  
On this issue in the short-term, as it is unlikely that large-scale changes will be started soon, I would close it, let thoughts coalesce and table it for later.

---

## Comment 22

> Username: pabristow  
> Created at: 2021-02-26 09:59:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-786542421  

All this is absolutely correct and I am *not* arguing for a change to the provision of implicit constructors from built-ins.  
  
I am concerned that users are and will find it difficult to *find where in their code* they are losing precision because they are inadvertently constructing from a lower precision type.  
  
My suggestion to allow users to switch off  *temporarily* construction from builtin is merely to help them diagnose their problem (or even potential problem) by flagging all the places where implicit construction is used.  
  
Or perhaps there is another way to highlight where reduction of precision may happen?

---

## Comment 23

> Username: ckormanyos  
> Created at: 2021-02-26 10:54:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/289#issuecomment-786572464  

> I am concerned that users are and will find it difficult to _find where in their code_ they are losing precision because they are inadvertently constructing from a lower precision type.  
  
That is a valid point.  
  
An easier, almost trivially outdated, method would be to support output of optional debug strings when a built-in floating-point constructor runs. But I would almost recommend developers do this locally when seeking this kind of problem.
