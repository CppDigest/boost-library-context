# #15 Add support for construction/assignment from two different integer ty… [Merged]

> Username: jzmaddock  
> Created at: 2017-03-27 07:26:09 UTC  
> Updated at: 2017-03-28 16:47:05 UTC  
> Merged at: 2017-03-27 18:42:34 UTC  
> Closed at: 2017-03-27 18:42:34 UTC  
> Url: https://github.com/boostorg/rational/pull/15  

…pes.  
  
Improve tests - check for mixed construction, and test domain_errors occur at the correct boundaries.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-03-27 07:29:59 UTC  
> Url: https://github.com/boostorg/rational/pull/15#issuecomment-289375077  

Edward, here's the PR for mixed-argument construction.  The header was pretty much fine after all with mostly just the "obvious" changes required.  The tests have been significantly enhanced to codify what we expect to happen when constructing from a different type to *this.  
I do notice that there are quite a few warnings from the header which really should be silenced, likewise C++14 constexpr support would be nice, but that's all for the future...

---

## Comment 2

> Username: eldiener  
> Created_at: 2017-03-27 14:15:04 UTC  
> Url: https://github.com/boostorg/rational/pull/15#issuecomment-289466514  

The tests pass but I am concerned that the new tests are only for positive numerators and denominators. Shouldn't this also work with mixed positive and negative numerators and denominators ? If so should we not have tests for mixed positive and negative numerators and denominators with mixed types, just to be complete ? Or am I being too picky ?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2017-03-27 17:25:12 UTC  
> Url: https://github.com/boostorg/rational/pull/15#issuecomment-289523626  

On 27/03/2017 15:15, Edward Diener wrote:  
>  
> The tests pass but I am concerned that the new tests are only for   
> positive numerators and denominators. Shouldn't this also work with   
> mixed positive and negative numerators and denominators ? If so should   
> we not have tests for mixed positive and negative numerators and   
> denominators with mixed types, just to be complete ? Or am I being too   
> picky ?  
>  
  
The code in BOOST_AUTO_TEST_CASE(conversions){...} pretty   
comprehensively tests both maximally positive and negative values, and   
then that incrementing/decrementing them results in the expected   
domain_error.  Likewise I hope, all the combinations of smaller/larger   
types and signed/unsigned variations are tested in there (ah not quite,   
just updated for the trivial cases where we're converting from a   
narrower type).  
  
What else am I missing?  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 4

> Username: eldiener  
> Created_at: 2017-03-27 18:42:26 UTC  
> Url: https://github.com/boostorg/rational/pull/15#issuecomment-289546326  

There are a few more variations I will add to the test but I will do so after the merge.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2017-03-28 07:45:28 UTC  
> Url: https://github.com/boostorg/rational/pull/15#issuecomment-289690234  

> Merged #15 <https://github.com/boostorg/rational/pull/15>.  
>  
  
Thanks!  
  
If we're being pedantic there is still one possible issue: namely, what   
should be the result of  
  
rational<short>(INT_MAX, INT_MAX/2)   ?  
  
Currently it will throw a domain error which is at least safe, where as   
previously it silently truncated the inputs and produced the incorrect   
result "1".  
  
We could however, change the code to construct a temporary   
rational<common_type<T, U>::type> and then convert from that if we   
wanted to support this corner case.  But I can't imagine many people   
being affected....  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 6

> Username: eldiener  
> Created_at: 2017-03-28 09:57:58 UTC  
> Url: https://github.com/boostorg/rational/pull/15#issuecomment-289721710  

I think we are doing the right thing even if we are theoretically "breaking" some code.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2017-03-28 16:47:05 UTC  
> Url: https://github.com/boostorg/rational/pull/15#issuecomment-289832151  

On 28/03/2017 10:57, Edward Diener wrote:  
>  
> I think we are doing the right thing even if we are theoretically   
> "breaking" some code.  
>  
  
+1.  
  
On second thoughts it also gets very complex if we try to normalize   
prior to assignment as there may not be a suitable safe common type   
available - for example if the args are mixed intmax_t and uintmax_t.  
  
So I think perhaps leaving it as it is is best.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---
