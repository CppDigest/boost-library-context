# #35 - BOOST_PP_SEQ_FOR_EACH regression [Closed]

> Username: Eddie-cz  
> Created at: 2020-11-16 22:36:47 UTC  
> Updated at: 2020-11-18 07:34:47 UTC  
> Closed at: 2020-11-18 07:34:47 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/35  

In boost versions prior to 1.75.0 beta 1, preprocessor expansion of following code led to output 2 3 4.  
In latest beta boost version, using gcc 10.2, or clang 11, this code leads to output 1 2 3, thus breaking compatibility with current  
code using r param.  
Also tested in msvc, both v142 and v141 toolsets correctly outputs 2 3 4.  
Code:  
#include <boost/preprocessor/library.hpp>  
  
#define MACRO(r, data, elem) r  
BOOST_PP_SEQ_FOR_EACH(MACRO,, (w)(x)(y))  
  
int main(int argc, char *argv[])  
{  
	return 0;  
}

---

## Comment 1

> Username: eldiener  
> Created at: 2020-11-17 00:32:39 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/35#issuecomment-728517612  

You are correct that this changed in 1.75 and should have been documented. This change was made in order to fix certain obscure bugs which occurred when working with numbers in FOR loops and the numbers involbved were nrear the maximim default limit of 256.

---

## Comment 2

> Username: Eddie-cz  
> Created at: 2020-11-17 15:21:51 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/35#issuecomment-728999598  

Thanks for explanation, I migrated to BOOST_PP_SEQ_FOR_EACH_I to have reliable param indexing across all platforms.

---

## Comment 3

> Username: eldiener  
> Created at: 2020-11-17 19:49:02 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/35#issuecomment-729162228  

I did not think that starting the FOR loop at 0 instead of 1, to allow the maximum number of FOR iterations, was going to cause any end-user problems, but it is a change from previous releases so I am sorry it did for you. Prior to the change the maximum number of the ternary macro invocations was actually one less than the maximum ( which defaults to 256 ), while following the change the maximum number of the ternary macro invocations matches the maximum. This limitation was actually in all previous releases but was never reported by anyone. I made the same sort of change for WHILE loops and REPEAT loops, in order to allow for the maximum number of ternary macro invocations.  
  
The practical reason for these changes are:  
  
BOOST_PP_FOR is used by seqs and lists, both of which default to 256 as the maximum number of elements.  
BOOST_PP_WHILE is used by almost all mathematical and logical operations, whose default maximum number is 256.  
BOOST_PP_REPEAT is used by all BOOST_PP_ENUM... functionality, whose default maximum number of enumerations is 256.

---

## Comment 4

> Username: Eddie-cz  
> Created at: 2020-11-17 20:10:34 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/35#issuecomment-729173396  

I definitely agree that current version is more logical. But IMHO that behaviour should be applied to MSVC compiler too, not only to clang 11 and gcc version 10. That's how we found out, code compiled under MSVC (still starts at 1) but was failing under clang-cl (now starts at 0). We used r param to separate macro params by comma (BOOST_PP_COMMA_IF(BOOST_PP_SUB(r, 2))...), using BOOST_PP_COMMA_IF(i) is much more readable and I suppose preferable.

---

## Comment 5

> Username: eldiener  
> Created at: 2020-11-17 22:36:41 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/35#issuecomment-729255627  

The problem with VC++'s default preprocessor is that it is largely non-compliant and extremely fragile, and I am just unwilling to touch it anymore in Boost PP. OTOH VC++'s new standard conforming preprocessor in VS Studio 2019 is C++ standard compliant, as is gcc and clang. You might consider using that.

---

## Comment 6

> Username: Eddie-cz  
> Created at: 2020-11-18 07:34:47 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/35#issuecomment-729494581  

Yey you are right, I forgot about new msvc preprocessor and understand you don't wanna mess with old legacy one, we will switch to new one. Closing this ticket, thanks.
