# #437 X3: Fixed wrong parse_rule signature instantiation [Merged]

> Username: Kojoley  
> Created at: 2019-01-13 16:08:01 UTC  
> Updated at: 2019-01-27 11:37:35 UTC  
> Merged at: 2019-01-26 14:32:05 UTC  
> Closed at: 2019-01-26 14:32:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/437  

When a rule has no attribute (leaves `Attribute` template parameter as default)  
the `BOOST_SPIRIT_INSTANTIATE` macro instantiates a `parse_rule` helper function  
with a wrong signature because `parse`, `phrase_parse`, and `skip_over` pass as  
the attribute `unused` variable which deduces to `unused_type const&` type, but  
`BOOST_SPIRIT_INSTANTIATE` instantiates `parse_rule` with `unused_type&` type.  
  
Used a trick to change `parse_rule` signature to take unsued_type by value when rule has no attribute (the signature `parse_rule(..., unsued_type)`).

---

## Comment 1

> Username: cppljevans  
> Created_at: 2019-01-13 18:08:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/437#issuecomment-453852150  

On 1/13/19 10:08 AM, Nikita Kniazev wrote:  
> When a rule has no attribute the `BOOST_SPIRIT_INSTANTIATE` macro instantiates  
> a `parse_rule` function with a wrong signature because `parse`, phrase_parse`,  
> and `skip_over` use `unused` variable which has `unused_type const` type, while  
> default template parameter for `rule` is non-const.  
>   
> There are three possible solutions:  
>   
>    1. Change default template parameter type of `rule` to `unused_type` const.  
>    2. Change `unused` variable type to non-const.  
>    3. Do not use `unused` for attributes, instead create a local variable  
>       of `unused_type` and pass it as the attribute.  
>   
> Forbidding non-const attributes is not a solution as it will require a check in  
> every parser and is simple to miss at adding new parsers.  
  
So, which solution was chosen?  From looking at detail/rule.hpp:29 here:  
  
https://github.com/boostorg/spirit/pull/437/files#diff-632a9a805644abb7caf7f726e70249a1R29  
  
I'm guessing 1 was chosen.  What's the advantage of 1 over 2?  
  
[snip]  
  
-regards,  
Larry

---

## Comment 2

> Username: Kojoley  
> Created_at: 2019-01-13 18:15:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/437#issuecomment-453852770  

> I'm guessing 1 was chosen.  
  
The PR did not yet merged, but yes, currently it implements the first way.  
  
>  What's the advantage of 1 over 2?  
  
It will not change the meaning of `unused` for everybody and will allow later to make it `constexpr`.

---

## Comment 3

> Username: cppljevans  
> Created_at: 2019-01-14 12:30:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/437#issuecomment-453989267  

On 1/13/19 12:15 PM, Nikita Kniazev wrote:  
>> I'm guessing 1 was chosen.  
>   
> The PR did not yet merged, but yes, currently it implements the first way.  
>   
[snip]  
Wouldn't this change the value of has_attribute here:  
  
https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/nonterminal/rule.hpp#L46  
  
How are you sure this change is harmless?  
  
-regards,  
Larry

---

## Comment 4

> Username: Kojoley  
> Created_at: 2019-01-14 14:25:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/437#issuecomment-454022069  

> Wouldn't this change the value of has_attribute here:  
>  
> /include/boost/spirit/home/x3/nonterminal/rule.hpp@develop#L46  
  
Thanks for spotting this.  
  
> How are you sure this change is harmless?  
  
I can't be *sure*, that's why there are tests and they all pass with the change. It may indicate multiple things like 1) that current tests are insufficient and do not cover the case that will be broken by the change 2) that the current state is already broken 3) there are redundant parts that are not actually used.  
  
Possibly and most likely `has_attribute` has no meaning when the actual attribute type is unused, that's my guess and I will investigate it.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2019-01-25 22:19:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/437#issuecomment-457751546  

Updated the PR with other solution. Used a trick to change `parse_rule` signature to take `unsued_type` by value when rule has no attribute (the signature `parse_rule(..., unsued_type)`).  
  
I think that `parse_rule` should NOT deduce attribute type (the attribute transformation must occur before it, not after), but this is out of this PR scope.

---

## Comment 6

> Username: djowel  
> Created_at: 2019-01-26 01:59:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/437#issuecomment-457791074  

LGTM!

---

## Comment 7

> Username: cppljevans  
> Created_at: 2019-01-27 10:22:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/437#issuecomment-457905648  

> When a rule has no attribute (leaves `Attribute` template parameter as default)  
> the `BOOST_SPIRIT_INSTANTIATE` macro instantiates a `parse_rule` helper function  
> with a wrong signature because `parse`, `phrase_parse`, and `skip_over` pass as  
> the attribute `unused` variable which deduces to `unused_type const&` type, but  
> `BOOST_SPIRIT_INSTANTIATE` instantiates `parse_rule` with `unused_type&` type.  
>   
> Used a trick to change `parse_rule` signature to take unsued_type by value when rule has no attribute (the signature `parse_rule(..., unsued_type)`).  
  
Implementing this "trick fix" requires using the complicated  
template metaprogramming shown in [this  
commit](https://github.com/boostorg/spirit/pull/437/commits/8d62e11fcc0c4bd9edb4df9677007556bb16943f)  
in nonterminal/rule.hpp in namespace detail.  
  
In view of complexity of that code, the simpler  
fix iii) mentioned in the original edit[OE] of [this comment](https://github.com/boostorg/spirit/pull/437#issue-244288155)  
would be a more maintainable solution.  To recap, fix iii) was:  
```  
  iii) Do not use `unused` for attributes, instead create a  
       local variable of `unused_type` and pass it as the  
       attribute.   
```  
Although fix iii) requires a local variable to replace every  
occurrence where `unused` is currently passed to a function  
expecting a non-const Attribute, the code in fix iii) would  
be **much** easier to read and understand than the  
complicated metaprogramming in the changes to  
nonterminal/rule.hpp in the above commit.  
  
Judging from this passage:  
```  
  `parse`, phrase_parse`, and `skip_over` use `unused`  
  variable  
```  
from your original edit[OE], you've already located the  
places where this would be done; hence, it should be easy to  
do.  
  
In summary, a maybe 2 line change at maybe just 3 places  
(the `parse`, `phrase_parse`, and `skip_over` mentioned in  
your [OE]) is much simpler than the multiline changes  
(including complicated template metaprograming) in the  
"trick fix"; hence, I'd urge you to reconsider using the  
fix iii).

---

## Comment 8

> Username: Kojoley  
> Created_at: 2019-01-27 11:37:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/437#issuecomment-457910266  

This is a bullet proof solution, it does not care about passed variable constness. I do not find the code complicated, it is just a bit ugly as any workaround. There are a lot of other places except the ones mentioned above where `unused` is passed as attribute.

---
