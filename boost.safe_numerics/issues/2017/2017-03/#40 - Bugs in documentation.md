# #40 - Bugs in documentation [Closed]

> Username: akrzemi1  
> Created at: 2017-03-14 17:13:36 UTC  
> Updated at: 2018-09-23 22:26:36 UTC  
> Closed at: 2018-08-14 16:34:39 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/40  

The following is the feedback from Steven Watanabe on documentation issues.   
  
There are two copies of proposal.pdf in the  
repository.  
  
safe_numerics.pdf seems to be out-dated.  
  
The include paths used in the documentation are a mess.  
I see at least 6 different variations:  
- boost/safe_numerics/include/  
- boost/safe_numerics/  
- safe/numeric/  
- boost/numeric/  
- safe_numerics/include/  
- ../include  
- ../../include  
  
### Introduction:  
  
"Since the problems and their solution are similar, We'll..."  
"We" should not be capitalized.  
  
"...the results of these operations are guaranteed to be  
either arithmetically correct or invoke an error"  
"either ... or ..." here has a noun on one side and  
a verb on the other.  Try "... either to be ... or to invoke ..."  
  
`#include <boost/safe_numeric/safe_integer.hpp>`  
I believe that this should be "safe_numerics"  
(with an "s")  
  
"Enforce of other program requirements using ranged integer types."  
Delete "of"  
  
"The library includes the types safe_range(Min, Max) and safe_literal(N)"  
These don't look like any C++ type I've ever seen unless  
they're macros.  
  
### tutorial/1.html  
  
"When this occurs, Most"  
lowercase "Most"  
  
"When this occurs, Most C++ compilers will continue to execute with no  
indication that the results are wrong"  
Is it the compiler that continues to execute or is it the  
program built by the compiler?  
  
"        // converts variables to int before evaluating he expression!"  
s/he/the/  
  
"The solution is to replace instances of char type with safe<char> type"  
There are no char's in this example.  
  
### tutorial/2.html:  
  
"This is a common problem with for loops"  
Is this really true?  The most common pattern  
of integer for loops is for(i=0;i<n;++i)  
which can only overflow when n is a wider  
type than i.  I usually see this with int/size_t,  
but that will get a signed/unsigned comparison  
warning on some compilers.  
  
### tutorial/5.html:  
  
Creating the bounds type is also somewhat error-prone.  
It would be quite easy for C++ programmers who are used  
to half-open ranges to use safe_unsigned_range<0, i_array.size()>  
  
### tutorial/7.html  
        std::cout << x / y;  
        std::cout << " error detected!" << std::endl;  
Should this be "NOT detected"?  
  
  
### eliminate_runtime_penalty/1.html:  
  
The links for native, automatic, and trap_exception are broken.  
  
### eliminate_runtime_penalty/3.html  
```  
    throw_exception // these variables need to  
```  
need to what?  
  
### integer.html:  
  
```std::numeric_limits<T>.is_integer```  
should be "::is_integer"  
  
### safe_numeric_concept.html:  
  
"However, it would be very surprising if any implementation were to be more complex that O(0);"  
I think you mean O(1).  O(0) means that it takes no time at all.  
  
"... all C++ operations permitted on it's base type..."  
s/it's/its/  
  
"( throw exception, compile time trap, etc..)"  
Delete the leading space.  
  
### promotion_policy.html:  
  
"auto sz = sx + y; // promotes expression type to a safe<long int> which  
requires no result checking  
is guaranteed not to overflow."  
  
The line wrapping ends up uncommented.  
  
### exception_policy.html:  
  
"EP | A type that full fills the requirements of an ExceptionPollicy"  
s/full fills/fulfills/, s/ExceptionPollicy/ExceptionPolicy/  
  
"EP::underflow_error(const char * message)"  
Underflow is a floating point error that happens  
when the value is too close to 0.  (INT_MIN - 1)  
is an overflow error, not an underflow error.  
  
```  
template<void (*F)(const char *), void (*G)(const char *), void(*H)(const char *)>  
boost::numeric::no_exception_support  
```  
What do the arguments mean?  According to the table,  
there a 6 functions, not 3.  
  
### safe.html:  
  
"T | Underlying type from which a safe type is being derived"  
"derived" has a specific meaning in C++ which is not what you mean.  
  
"...at runtime if any of several events result in an incorrect  
arithmetic type."  
It isn't the type that's incorrect, is it?  
  
"If one wants to switch between save and built-in types ..."  
s/save/safe/  
  
"this type of code will have to be fixed so that implicit  
conversions to built-in types do not occur"  
So are explicit casts allowed?  
  
"This is because there is no way to guarantee that the expression i * i"  
The second 'i' seems to be outside the `<code>` block.  
  
"This can be justified by the observe"  
This sentence is incomplete.  
  
"This can be done by specifying a non-default type promotion policy  
automatic"  
Should be "policy, automatic."  Also, automatic should probably be `<code>`  
  
"All the work is done at compile time - checking for exceptions  
necessary (input is of course an exception)"  
I can't parse this sentence.  Also, the '-' should  
be an em-dash, I think.  
  
### safe_range.html:  
  
"MIN | must be non-integer literal"  
I think this is supposed to be "non-negative integer"  
Also, this is only true for safe_unsigned_range.  
It would by weird if safe_signed_range didn't  
accept negative arguments.  
  
`#include <safe/numeric/safe_range.hpp>`  
The directory safe/numeric/ doesn't exist.  
  
    safe_unsigned_range<7, 24> i  
missing ';'  
  
static_assert(std::is_same<decltype(k), safe<unsigned int>);  
Syntax error: expected '>' befor ')'  
  
Also, safe is defined in safe_integer.hpp which is not #included.  
  
### promotion_policies/native.html  
  
"It's main function is to trap incorrect..."  
s/It's/Its/  
  
### promotion_policies/cpp.html  
  
"...we can force the evaluation of C++ expressions test environment..."  
"...expressions /in the/ test..."  
  
Unless I'm misunderstanding something, the use  
of trap_exception will fail to compile at:  
d *= velocity;  
  
"Note the usage of the compile time trap policy in order to  
detect at compile time any possible error conditions. As I  
write this, this is still being refined. Hopefully this will  
be available by the time you read this."  
This comment seems out-dated.  
  
### exception_policies.html:  
  
"no_exception_support<O, U = O, R =O, D = O>"  
So now we have 4 parameters?  
  
### exception_policies/trap_exception.html:  
  
"This exception policy will trap at compile time any  
operation COULD result in a runtime exception"  
There's a missing word here.  "...compile time /if/ any..."  
is one possible fix.  
  
### exception_policies/no_exception_support.html  
  
Template Parameters table: waaaaaay too many O's.  
Also, the first two parameters (no exception and  
uninitialized) are missing.  
  
### library_implementation.html:  
  
"correct bugs in it, or understand it's limitations"  
s/it's/its/  
  
### interval.html:  
  
boost/numeric/interval.hpp and boost::numeric::interval  
are already taken by Boost.Interval.  
  
### checked_result.html:  
  
"checked_result(e, msg)"  
'e' was not defined.  
  
"static_cast<const char *>(c) | R | extract wrapped value - asserts if  
not possible"  
I think this is supposed to return the message as 'const char *'  
not the value as 'R'.  
  
### exception_type.html:  
  
"dispatch(overflow_error, "operation resulted in overflow");"  
The template parameter EP is missing.  
  
### rationale.html:  
  
"2. Can safe types be used as drop-in replacement for built-in types?"  
replacements (plural)  
  
```  
#include  <cstdint>  
typedef safe_t<std::int16_t> int16_t;  
```  
This may or may not compile as it is unspecified whether  
cstdint defines ::int16_t.

---

## Comment 1

> Username: robertramey  
> Created at: 2017-03-14 17:47:30 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/40#issuecomment-286503791  

I've got all this and am working on it as we speak.  Actually I have all   
the feed back from everyone.  Almost all of it will be addressed in some   
way.  
  
a) bugs - will be fixed  
  
b) documentation errors - will be fixed  
  
c) sticky design choices - may be modified or if not added to the   
rational in the documentation  
  
d) documentation enhancements - e.g. showing the console output in the   
tutorial examples.  
  
e) package enhancements - if they are minor, they might be considered.    
Not really much here  
  
exception - saturation.  I wouldn't have considered this. But someone   
showed how it could be accomplished with some slight changes in the   
interface for promotion policies.  Very clever - I won't include as part   
of the library explicitly.  But it's possible I might included it as a   
case study on creating one's own promotion policy.  
  
I've also been considering another extended case study on using the cpp   
promotion policy to test/prove a sample stepper motor control program   
for an 8 bit processor. This is an example that is very close to my   
heart. This is too extensive for a tutorial example but not part of the   
library itself.  I'm so interested in this that I'm thinking a   
submitting a talk to CPPCon 2017 built around this case study. I tried   
to make it before, but it became clear that it was too ambitious for a   
tutorial example so I set it aside.  Lately I was playing with it some   
and became more motivated to finish it off.  
  
The suggestion of replacing safe_..._Iiteral with std::integral_constant   
is very interesting and I want to explore further.  My preliminary view   
is that it's a very good idea.  specializing a few traits like is_safe   
should be about all that is needed.  replace safe_literal with   
std::integral constant would diminish the "surface area" of the API   
because people already know and use integral constant.  
  
Note that in the past the Review manager has made his report and   
conditions.  And often considerable time would pass before the library   
got actually added to boost - since what seem small changes tend to have   
unintended consequences and the time required is ofter more than one   
would expect.  And people have jobs to.  Fortunately, I'm unemployed.  
  
Acceptance of the library is important to me of course.  But more   
important is to have visible/tangible effect of C++ development. This   
worked well for me with the serialization library.  I think that library   
is widely used and that even newer alternatives use the same API and   
actually depend upon it's documentation.  
  
This library is a "harder sell" but I've come to believe that this is a   
much bigger and more important topic than I first appreciated.  This   
boost review is important but it's not the end for me.  It's only the   
beginning.  
  
  
On 3/14/17 10:13 AM, Andrzej Krzemieński wrote:  
>  
> The following is the feedback from Steven Watanabe on documentation   
> issues.  
>  
> There are two copies of proposal.pdf in the  
> repository.  
>  
> safe_numerics.pdf seems to be out-dated.  
>  
> The include paths used in the documentation are a mess.  
> I see at least 6 different variations:  
>  
>   * boost/safe_numerics/include/  
>   * boost/safe_numerics/  
>   * safe/numeric/  
>   * boost/numeric/  
>   * safe_numerics/include/  
>   * ../include  
>   * ../../include  
>  
>  
>       Introduction:  
>  
> "Since the problems and their solution are similar, We'll..."  
> "We" should not be capitalized.  
>  
> "...the results of these operations are guaranteed to be  
> either arithmetically correct or invoke an error"  
> "either ... or ..." here has a noun on one side and  
> a verb on the other. Try "... either to be ... or to invoke ..."  
>  
> |#include <boost/safe_numeric/safe_integer.hpp>|  
> I believe that this should be "safe_numerics"  
> (with an "s")  
>  
> "Enforce of other program requirements using ranged integer types."  
> Delete "of"  
>  
> "The library includes the types safe_range(Min, Max) and safe_literal(N)"  
> These don't look like any C++ type I've ever seen unless  
> they're macros.  
>  
>  
>       tutorial/1.html  
>  
> "When this occurs, Most"  
> lowercase "Most"  
>  
> "When this occurs, Most C++ compilers will continue to execute with no  
> indication that the results are wrong"  
> Is it the compiler that continues to execute or is it the  
> program built by the compiler?  
>  
> " // converts variables to int before evaluating he expression!"  
> s/he/the/  
>  
> "The solution is to replace instances of char type with safe type"  
> There are no char's in this example.  
>  
>  
>       tutorial/2.html:  
>  
> "This is a common problem with for loops"  
> Is this really true? The most common pattern  
> of integer for loops is for(i=0;i<n;++i)  
> which can only overflow when n is a wider  
> type than i. I usually see this with int/size_t,  
> but that will get a signed/unsigned comparison  
> warning on some compilers.  
>  
>  
>       tutorial/5.html:  
>  
> Creating the bounds type is also somewhat error-prone.  
> It would be quite easy for C++ programmers who are used  
> to half-open ranges to use safe_unsigned_range<0, i_array.size()>  
>  
>  
>       tutorial/7.html  
>  
> |std::cout << x / y; std::cout << " error detected!" << std::endl; |  
>  
> Should this be "NOT detected"?  
>  
>  
>       eliminate_runtime_penalty/1.html:  
>  
> The links for native, automatic, and trap_exception are broken.  
>  
>  
>       eliminate_runtime_penalty/3.html  
>  
> |throw_exception // these variables need to |  
>  
> need to what?  
>  
>  
>       integer.html:  
>  
> |std::numeric_limits<T>.is_integer|  
> should be "::is_integer"  
>  
>  
>       safe_numeric_concept.html:  
>  
> "However, it would be very surprising if any implementation were to be   
> more complex that O(0);"  
> I think you mean O(1). O(0) means that it takes no time at all.  
>  
> "... all C++ operations permitted on it's base type..."  
> s/it's/its/  
>  
> "( throw exception, compile time trap, etc..)"  
> Delete the leading space.  
>  
>  
>       promotion_policy.html:  
>  
> "auto sz = sx + y; // promotes expression type to a safe which  
> requires no result checking  
> is guaranteed not to overflow."  
>  
> The line wrapping ends up uncommented.  
>  
>  
>       exception_policy.html:  
>  
> "EP | A type that full fills the requirements of an ExceptionPollicy"  
> s/full fills/fulfills/, s/ExceptionPollicy/ExceptionPolicy/  
>  
> "EP::underflow_error(const char * message)"  
> Underflow is a floating point error that happens  
> when the value is too close to 0. (INT_MIN - 1)  
> is an overflow error, not an underflow error.  
>  
> |template<void (*F)(const char *), void (*G)(const char *),   
> void(*H)(const char *)> boost::numeric::no_exception_support |  
>  
> What do the arguments mean? According to the table,  
> there a 6 functions, not 3.  
>  
>  
>       safe.html:  
>  
> "T | Underlying type from which a safe type is being derived"  
> "derived" has a specific meaning in C++ which is not what you mean.  
>  
> "...at runtime if any of several events result in an incorrect  
> arithmetic type."  
> It isn't the type that's incorrect, is it?  
>  
> "If one wants to switch between save and built-in types ..."  
> s/save/safe/  
>  
> "this type of code will have to be fixed so that implicit  
> conversions to built-in types do not occur"  
> So are explicit casts allowed?  
>  
> "This is because there is no way to guarantee that the expression i * i"  
> The second 'i' seems to be outside the |<code>| block.  
>  
> "This can be justified by the observe"  
> This sentence is incomplete.  
>  
> "This can be done by specifying a non-default type promotion policy  
> automatic"  
> Should be "policy, automatic." Also, automatic should probably be |<code>|  
>  
> "All the work is done at compile time - checking for exceptions  
> necessary (input is of course an exception)"  
> I can't parse this sentence. Also, the '-' should  
> be an em-dash, I think.  
>  
>  
>       safe_range.html:  
>  
> "MIN | must be non-integer literal"  
> I think this is supposed to be "non-negative integer"  
> Also, this is only true for safe_unsigned_range.  
> It would by weird if safe_signed_range didn't  
> accept negative arguments.  
>  
> |#include <safe/numeric/safe_range.hpp>|  
> The directory safe/numeric/ doesn't exist.  
>  
> |safe_unsigned_range<7, 24> i |  
>  
> missing ';'  
>  
> static_assert(std::is_same<decltype(k), safe);  
> Syntax error: expected '>' befor ')'  
>  
> Also, safe is defined in safe_integer.hpp which is not #included.  
>  
>  
>       promotion_policies/native.html  
>  
> "It's main function is to trap incorrect..."  
> s/It's/Its/  
>  
>  
>       promotion_policies/cpp.html  
>  
> "...we can force the evaluation of C++ expressions test environment..."  
> "...expressions /in the/ test..."  
>  
> Unless I'm misunderstanding something, the use  
> of trap_exception will fail to compile at:  
> d *= velocity;  
>  
> "Note the usage of the compile time trap policy in order to  
> detect at compile time any possible error conditions. As I  
> write this, this is still being refined. Hopefully this will  
> be available by the time you read this."  
> This comment seems out-dated.  
>  
>  
>       exception_policies.html:  
>  
> "no_exception_support<O, U = O, R =O, D = O>"  
> So now we have 4 parameters?  
>  
>  
>       exception_policies/trap_exception.html:  
>  
> "This exception policy will trap at compile time any  
> operation COULD result in a runtime exception"  
> There's a missing word here. "...compile time /if/ any..."  
> is one possible fix.  
>  
>  
>       exception_policies/no_exception_support.html  
>  
> Template Parameters table: waaaaaay too many O's.  
> Also, the first two parameters (no exception and  
> uninitialized) are missing.  
>  
>  
>       library_implementation.html:  
>  
> "correct bugs in it, or understand it's limitations"  
> s/it's/its/  
>  
>  
>       interval.html:  
>  
> boost/numeric/interval.hpp and boost::numeric::interval  
> are already taken by Boost.Interval.  
>  
>  
>       checked_result.html:  
>  
> "checked_result(e, msg)"  
> 'e' was not defined.  
>  
> "static_cast<const char *>(c) | R | extract wrapped value - asserts if  
> not possible"  
> I think this is supposed to return the message as 'const char *'  
> not the value as 'R'.  
>  
>  
>       exception_type.html:  
>  
> "dispatch(overflow_error, "operation resulted in overflow");"  
> The template parameter EP is missing.  
>  
>  
>       rationale.html:  
>  
> "2. Can safe types be used as drop-in replacement for built-in types?"  
> replacements (plural)  
>  
> |#include <cstdint> typedef safe_t<std::int16_t> int16_t; |  
>  
> This may or may not compile as it is unspecified whether  
> cstdint defines ::int16_t.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/robertramey/safe_numerics/issues/40>, or mute the   
> thread   
> <https://github.com/notifications/unsubscribe-auth/AB4R3Jcsr-c_UUJAPSp6t7ywlMnGyAg1ks5rlsrBgaJpZM4Mc4iE>.  
>  
  
--   
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 2

> Username: robertramey  
> Created at: 2018-08-12 14:41:40 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/40#issuecomment-412347324  

safe_numeric_concept.html:  
  
"However, it would be very surprising if any implementation were to be more complex that O(0);"  
I think you mean O(1). O(0) means that it takes no time at all.  
  
Hmmm - to me O(0) - means a fixed time independent of the number of instances.  But looking at the context - "complexity guarantee" doesn't make a lot of sense as there is no scaling variable.  I think I'll just eliminate the section  
  
promotion_policies/cpp.html - still needs an update  
  
boost/numeric/interval.hpp and boost::numeric::interval  
are already taken by Boost.Interval. - still pending  
  
rational2.html - still pending

---

## Comment 3

> Username: robertramey  
> Created at: 2018-08-14 16:34:39 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/40#issuecomment-412935271  

rational2.html  I think this is fixed - just forget to note it.  
  
boost/numeric/interval.hpp and boost::numeric::interval  
are already taken by Boost.Interval. - still pending  
  
Right - still pending.  It's unfortunate that boost::interval isn't constexpr - oh well
