# #271 Spirit accidentally relays on `types` typedef of `boost::optional` [Merged]

> Username: Kojoley  
> Created at: 2017-11-10 19:08:02 UTC  
> Updated at: 2017-11-13 14:21:50 UTC  
> Merged at: 2017-11-13 14:21:41 UTC  
> Closed at: 2017-11-13 14:21:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/271  

Replaces #201, fixes #249.  
  
New `boost:optional` implementation does not contain `types` what caused a compilation error for Qi and wrong results for Karma.  
  
Other problem is that `not_is_variant<optional<variant<T...>>>` results in `mpl::false_` what is unclear from it's name. While Karma relays on exactly this behavior, Qi wrongly considers that it `mpl::true_`. I have fixed this name ambiguity and updated Karma for the new `not_is_variant` behavior.  
  
P.S: I am bad at naming things but I do not see any shorter name for `not_is_variant_or_variant_in_optional`

---

## Comment 1

> Username: Kojoley  
> Created_at: 2017-11-10 19:14:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/271#issuecomment-343560947  

@djowel merge this if you are okay with `not_is_variant_or_variant_in_optional` name

---

## Comment 2

> Username: Kojoley  
> Created_at: 2017-11-10 21:02:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/271#issuecomment-343584899  

Also closes https://svn.boost.org/trac10/ticket/12349

---

## Comment 3

> Username: Kojoley  
> Created_at: 2017-11-13 14:21:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/271#issuecomment-343932894  

I have self-merged this because it works for me. Tested on VS2017, clang 3.9/5.0 c++03/1y, gcc 4.8.4/6 c++03/1y.

---
