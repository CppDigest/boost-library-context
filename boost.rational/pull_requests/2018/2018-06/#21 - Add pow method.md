# #21 Add pow method [Merged]

> Username: jeking3  
> Created at: 2018-06-14 12:47:05 UTC  
> Updated at: 2018-07-11 19:23:21 UTC  
> Merged at: 2018-06-18 01:18:02 UTC  
> Closed at: 2018-06-18 01:18:03 UTC  
> Url: https://github.com/boostorg/rational/pull/21  

The original author of #9 deleted their repository.  I applied the change manually.  
  
This closes #9

---

## Review 1 [Commented]

> Username: mclow  
> Created_at: 2018-06-14 13:46:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/rational/pull/21#pullrequestreview-128796290  

📁 include/boost/rational.hpp

```diff
 147 |     typedef IntType (helper::* bool_type)[2];
 148 | 
 149 |+     class already_normalized {};
```

> Username: mclow  
> Created_at: 2018-06-14 13:46:21 UTC  
> Updated_at: 2018-06-17 12:28:57 UTC  
> Url: https://github.com/boostorg/rational/pull/21#discussion_r195428859  

Should this be a public constructor?

> Username: jeking3  
> Created_at: 2018-06-14 19:39:13 UTC  
> Updated_at: 2018-06-17 12:28:57 UTC  
> Url: https://github.com/boostorg/rational/pull/21#discussion_r195546776  

I think we can leave it this way for now (private).

> Username: jeking3  
> Created_at: 2018-06-17 12:11:17 UTC  
> Updated_at: 2018-06-17 12:28:57 UTC  
> Url: https://github.com/boostorg/rational/pull/21#discussion_r195927644  

It turns out this is no longer necessary with the removal of reciprocal().


---

## Comment 2

> Username: mclow  
> Created_at: 2018-06-14 13:49:28 UTC  
> Url: https://github.com/boostorg/rational/pull/21#issuecomment-397302533  

Other than my one comment, this looks fine. I'd be really tempted to remove the Metrowerks 8.3 workarounds, though. That compiler was released in 2002/2003.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2018-06-14 15:03:55 UTC  
> Updated_at: 2018-06-17 16:52:54 UTC  
> Url: https://github.com/boostorg/rational/pull/21#issuecomment-397328431  

# [Codecov](https://codecov.io/gh/boostorg/rational/pull/21?src=pr&el=h1) Report  
> Merging [#21](https://codecov.io/gh/boostorg/rational/pull/21?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/rational/commit/70fe05040b65665def7440f9be534994a35812ed?src=pr&el=desc) will **decrease** coverage by `0.26%`.  
> The diff coverage is `58.33%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/rational/pull/21/graphs/tree.svg?width=650&height=150&src=pr&token=GWRPyIttVH)](https://codecov.io/gh/boostorg/rational/pull/21?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #21      +/-   ##  
===========================================  
- Coverage    67.52%   67.25%   -0.27%       
===========================================  
  Files            1        1                
  Lines          271      281      +10       
  Branches        90       97       +7       
===========================================  
+ Hits           183      189       +6       
  Misses           3        3                
- Partials        85       89       +4  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/rational/pull/21?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/rational.hpp](https://codecov.io/gh/boostorg/rational/pull/21/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9yYXRpb25hbC5ocHA=) | `67.25% <58.33%> (-0.27%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/rational/pull/21?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/rational/pull/21?src=pr&el=footer). Last update [70fe050...20ea6ba](https://codecov.io/gh/boostorg/rational/pull/21?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: swatanabe  
> Created_at: 2018-06-14 15:24:42 UTC  
> Url: https://github.com/boostorg/rational/pull/21#issuecomment-397335560  

AMDG  
  
On 06/14/2018 06:47 AM, James E. King III wrote:  
> @jeking3 requested your review on: boostorg/rational#21 Add pow and reciprocal methods.  
>   
  
I'm somewhat unconvinced of the value of x.reciprocal()  
as opposed to 1/x, which I believe is more clear.  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: jeking3  
> Created_at: 2018-06-14 20:18:44 UTC  
> Url: https://github.com/boostorg/rational/pull/21#issuecomment-397425215  

@swatanabe I'm not sure I understand what you mean by the `1/x` in the boost::rational context.  Without a reciprocal() call, the only way I see to do it otherwise is to call `1.0/rational_cast<double>(r)`, and then you are stuck with a double and no good way to get back to a rational<>IntType> again.

---

## Comment 6

> Username: swatanabe  
> Created_at: 2018-06-16 13:42:44 UTC  
> Url: https://github.com/boostorg/rational/pull/21#issuecomment-397813028  

AMDG  
  
On 06/14/2018 02:18 PM, James E. King III wrote:  
> @swatanabe I'm not sure I understand what you mean by the `1/x` in the boost::rational context.  Without a reciprocal() call, the only way I see to do it otherwise is to call `1.0/rational_cast<double>(r)`, and then you are stuck with a double and no good way to get back to a rational<>IntType> again.  
>   
  
Why would you need to use double?  There's  
an operator/(CompatibleIntType, rational<IntType>)  
  
In Christ,  
Steven Watanabe

---

## Comment 7

> Username: jeking3  
> Created_at: 2018-06-16 19:29:58 UTC  
> Url: https://github.com/boostorg/rational/pull/21#issuecomment-397834277  

I removed reciprocal() and used your suggestion, so the addition is just pow(..) now.

---

## Comment 8

> Username: jeking3  
> Created_at: 2018-07-11 18:20:24 UTC  
> Updated_at: 2018-07-11 19:23:21 UTC  
> Url: https://github.com/boostorg/rational/pull/21#issuecomment-404264480  

It looks like this may have caused some issues in poorly scoped calls to pow() - see:  
https://github.com/qtumproject/cpp-eth-qtum/issues/31  
  
Any suggestions?  Should Boost.Spirit be more specific?  
Does pow need to use SFINAE to disable itself like the operators?  
I think pow is being defined in namespace boost which means all other boost libraries will pick it up if they include the header and then call an unqualified pow(...).

---
