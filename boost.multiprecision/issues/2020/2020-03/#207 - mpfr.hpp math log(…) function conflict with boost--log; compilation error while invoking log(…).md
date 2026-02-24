# #207 - mpfr.hpp math log(…) function conflict with boost::log; compilation error while invoking log(…) [Closed]

> Username: cosurgi  
> Created at: 2020-03-30 16:42:50 UTC  
> Updated at: 2021-03-14 21:13:04 UTC  
> Closed at: 2020-04-05 12:53:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/207  

This line won't compile:  
  
https://github.com/boostorg/multiprecision/blob/b46a0ecbe5301858ef9016ea6e2e92e2afc399c5/include/boost/multiprecision/mpfr.hpp#L2246  
  
My guess is that only `eval_log(…)` is available there. I encountered this problem in yade pipeline with boost 1.71: https://gitlab.com/yade-dev/trunk/-/jobs/491138134  
  
```  
/usr/include/boost/multiprecision/mpfr.hpp: In function 'boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<digits10, AllocationType>, ExpressionTemplates> boost::math::lgamma(boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<digits10, AllocationType>, ExpressionTemplates>, int*, const Policy&)':  
 /usr/include/boost/multiprecision/mpfr.hpp:2233:22: error: expected primary-expression before '(' token  
  2233 |          result = log(boost::math::constants::pi<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<Digits10, AllocateType>, ExpressionTemplates> >()) - lgamma(arg, 0, pol) - log(t);  
       |                      ^  
 /usr/include/boost/multiprecision/mpfr.hpp:2233:200: error: expected primary-expression before '(' token  
  2233 |          result = log(boost::math::constants::pi<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<Digits10, AllocateType>, ExpressionTemplates> >()) - lgamma(arg, 0, pol) - log(t);  
       |                                                                                                                                                                                                        ^  
```  
  
  
~~I think that replacing it with `eval_log(…)` fixes the problem~~. At least this works for me, and test invocations of lgamma pass `yade --test`. I will prepare a PR with a fix.  
  
EDIT: adding `using boost::multiprecision::log;` fixes conflict with `boost::log` namespace.

---

## Comment 1

> Username: cosurgi  
> Created at: 2020-03-30 16:51:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/207#issuecomment-606116010  

Also see: https://gitlab.com/yade-dev/trunk/-/merge_requests/450

---

## Comment 2

> Username: cosurgi  
> Created at: 2020-03-30 19:35:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/207#issuecomment-606202016  

Boost 1.71 is about to be included in Ubuntu LTS 20.04. Without the fix for this yade using MPFR will be impossible for next 5 years on ubuntu. I understand it's late, but there is still some chance that this bugfix will enter ubuntu 20.04 if you accept it here.  
  
I tried to implement a workaround for that, but without success so far.

---

## Comment 3

> Username: cosurgi  
> Created at: 2020-03-30 19:44:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/207#issuecomment-606206436  

@jzmaddock can you have a look at this?

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-03-31 08:31:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/207#issuecomment-606479522  

Whatever the issue is, it's not what you think!  
  
This code:  
  
```  
#include <iostream>  
#include <boost/multiprecision/mpfr.hpp>  
  
  
int main()  
{  
   boost::multiprecision::mpfr_float_50 f(-2.5);  
   std::cout << boost::math::lgamma(f) << std::endl;  
}  
  
```  
  
Works just fine for me, and executes the "problem" code.  Do you have a reduced test case?

---

## Comment 5

> Username: cosurgi  
> Created at: 2020-03-31 15:07:55 UTC  
> Updated at: 2020-03-31 16:11:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/207#issuecomment-606686438  

Thank you! This is good news, why didn't I try that. Maybe I was too tired, sorry.  
  
I have checked the order of includes, and guess what, here's your minimal failing example:  
  
```  
#include <iostream>  
#include <boost/log/expressions.hpp>  
#include <boost/multiprecision/mpfr.hpp>  
  
int main()  
{  
	boost::multiprecision::mpfr_float_50 f(-2.5);  
	std::cout << std::setprecision(50) << boost::math::lgamma(f) << std::endl;  
}  
```  
  
because in yade I am using the [boost::log](https://www.boost.org/doc/libs/1_72_0/libs/log/doc/html/index.html) framework for logging messages!  
  
I don't know what to do with that conflict here :) But now I can fix yade :)  
  
EDIT: I used [this workaround in yade](https://gitlab.com/yade-dev/trunk/-/merge_requests/455/diffs?commit_id=2b1a3e3df5be5c4325018d6fc06c6133f47b4943)

---

## Comment 6

> Username: jzmaddock  
> Created at: 2020-03-31 16:25:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/207#issuecomment-606733523  

Ugh, looks like the declaration of boost::log is poisoning ADL in this case.  
  
I can fix this one by making the calls fully qualified, but any functions that really do rely on ADL are probably broken by this :(

---

## Comment 7

> Username: cosurgi  
> Created at: 2020-03-31 16:33:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/207#issuecomment-606737549  

Yes,  I am concerned with that. I heavily rely on ADL in yade. I had to do these [inline function call redirections](https://gitlab.com/yade-dev/trunk/-/blob/master/lib/high-precision/MathFunctions.hpp), because every multiprecision type had its own quirks, and that was the only way to make them work all together.  
  
However in this commit maybe it is correct, because it is inside a very templated function (4 template parameters), so you know exactly the type which you want to call. My ::yade::math::log(…) ends up calling exactly the same function.  
  
On the other hand maybe it's worth pinging the boost::log maintainers?

---

## Comment 8

> Username: cosurgi  
> Created at: 2020-03-31 16:43:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/207#issuecomment-606742884  

nah, it's not worth pinging them. They can't change the namespace `boost::log` name ;) Also, this bug wasn't here in boost version 1.67. Why it's not affecting in other places, but only here?

---

## Comment 9

> Username: cosurgi  
> Created at: 2020-03-31 17:14:43 UTC  
> Updated at: 2020-03-31 17:15:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/207#issuecomment-606759521  

I checked [your fix](https://github.com/boostorg/multiprecision/pull/209/files#diff-3a0e15b18cd4424ea9c527dbf5081350) on ubuntu focal 20.04 and it works.  
  
But I have also checked a simpler fix, just add instead:  
  
```  
         using boost::multiprecision::log;  
```  
  
in there. This one does not break ADL, and works too!

---

## Comment 10

> Username: jzmaddock  
> Created at: 2020-03-31 17:36:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/207#issuecomment-606770531  

Yup, just tried that myself and it also works.  
  
Interestingly the error message from msvc indicates that this is not a deduced context and that ADL does not apply: I don't understand why that would be, but it seems to be the way all compilers are treating this.  Which I guess means it doesn't really matter which fix is applied.

---

## Comment 11

> Username: ofloveandhate  
> Created at: 2021-03-14 20:58:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/207#issuecomment-798978571  

So, I got bit by this bug.  Boost 1.71 as distributed via `apt` in Ubuntu LTS 20.04 has this.  I'm hoping you could provide a suggestion on a course of action for dealing with this.  Should I expect my users, compiling from source, to modify Boost.Multiprecision's source file?  Should I use a submodule of my Git repo, so that the most current stable version of Multiprecision is used?  Thanks for any advice, or for redirecting me to a better place to ask this question if there is one.

---

## Comment 12

> Username: cosurgi  
> Created at: 2021-03-14 21:13:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/207#issuecomment-798980796  

you can use the same workaround as me, include logarithm before logging: https://gitlab.com/yade-dev/trunk/-/blob/master/lib/base/Logging.hpp#L30
