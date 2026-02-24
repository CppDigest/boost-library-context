# #13 - Non-free usage requirements are not allowed [Closed]

> Username: swatanabe  
> Created at: 2018-01-06 21:48:31 UTC  
> Updated at: 2018-06-01 20:25:04 UTC  
> Closed at: 2018-06-01 20:25:04 UTC  
> Url: https://github.com/boostorg/dll/issues/13  

https://github.com/boostorg/dll/blob/develop/test/Jamfile.v2#L22  
  
Boost.Build doesn't currently check for this, but it's likely to behave very strangely.  
(i.e. both debug and release will be present.)

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-04-15 16:54:18 UTC  
> Url: https://github.com/boostorg/dll/issues/13#issuecomment-381420973  

What's the best way to solve this? (If there's none, I'm OK with dropping the '<variant>release')

---

## Comment 2

> Username: swatanabe  
> Created at: 2018-04-15 17:16:44 UTC  
> Url: https://github.com/boostorg/dll/issues/13#issuecomment-381422462  

AMDG  
  
On 04/15/2018 10:54 AM, Antony Polukhin wrote:  
> What's the best way to solve this? (If there's none, I'm OK with dropping the '<variant>release')  
>   
  
  I'm not sure what exactly you're trying to achieve with this.  
It should work if you put <variant>release as a requirement  
of the specific tests instead.  
  
The rules for properties are something like:  
- If a target is built with a propagated property  
  (like <variant>release) then all its dependencies  
  will be built with the same value of that property.  
- If target A depends on target B, directly or indirectly  
  then A will be built with the properties in the  
  usage-requirements of B.  
  
  The logical conclusion of these rules is that propagated  
features in usage-requirements will be applied to the  
whole dependency graph, regardless of where they appear,  
which is a surprising and impractical result.  I have no  
idea how to resolve this in a consistent way, so such  
usage-requirements will simply be ignored, with a warning.  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: apolukhin  
> Created at: 2018-05-24 17:57:28 UTC  
> Url: https://github.com/boostorg/dll/issues/13#issuecomment-391804931  

The intend was to put those flags into the library build requirements, not into the usage requirements. Probably this time I've done it right in 5fc62a9.
