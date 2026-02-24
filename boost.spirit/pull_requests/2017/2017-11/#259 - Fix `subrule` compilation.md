# #259 Fix `subrule` compilation [Merged]

> Username: Kojoley  
> Created at: 2017-11-03 17:01:09 UTC  
> Updated at: 2017-11-16 00:29:51 UTC  
> Merged at: 2017-11-13 14:15:58 UTC  
> Closed at: 2017-11-13 14:15:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/259  

This fixes a long standing bugs persisted in `subrule` for years.  
Those `%=` operator overloads are awful, but I do not know other solution (It should be possible to [tell proto to disable operators](http://www.boost.org/doc/libs/1_65_1/doc/html/proto/users_guide.html#boost_proto.users_guide.front_end.customizing_expressions_in_your_domain.inhibiting_overloads) but I do not know spirit's internals well enough for doing that).  
  
Tested on VS2017, clang 5.0 c++03/1y, gcc 6 c++03/1y

---

## Comment 1

> Username: Kojoley  
> Created_at: 2017-11-03 17:08:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/259#issuecomment-341767286  

It took me several days to figure out the operators problem I really hope there is a simple method of disabling unused `proto` operators for `spirit` expressions.

---

## Comment 2

> Username: djowel  
> Created_at: 2017-11-03 23:56:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/259#issuecomment-341854719  

Wow, you really know Spirit! :-) I assume all tests are good with this patch?  
  
BTW, would you like to be an active maintainer? Can't find your email in github, but feel free to email me at djowel-at-gmail-dot-com

---

## Comment 3

> Username: Kojoley  
> Created_at: 2017-11-04 15:32:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/259#issuecomment-341906014  

> Wow, you really know Spirit! :-)  
  
Nah, I am still bad, but I make progress.  
  
> I assume all tests are good with this patch?  
  
Yes, with this patch `repository/tests` now finally fixed https://travis-ci.org/Kojoley/spirit/builds/296877440  
  
> BTW, would you like to be an active maintainer?  
  
It will make configuring CI stuff for Spirit repository easier for me, so yes, thanks for the offer!

---

## Comment 4

> Username: Kojoley  
> Created_at: 2017-11-13 14:16:03 UTC  
> Url: https://github.com/boostorg/spirit/pull/259#issuecomment-343931302  

I have self-merged this because it works for me and cannot break already broken.

---

## Review 5 [Commented]

> Username: vtnerd  
> Created_at: 2017-11-16 00:24:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/259#pullrequestreview-76976781  

📁 include/boost/spirit/repository/home/karma/nonterminal/subrule.hpp

```diff
 471 | 
 472 |         // non-const versions needed to suppress proto's %= kicking in
 473 |+ #ifndef BOOST_NO_CXX11_RVALUE_REFERENCES
```

> Username: vtnerd  
> Created_at: 2017-11-16 00:24:55 UTC  
> Updated_at: 2017-11-16 00:26:11 UTC  
> Url: https://github.com/boostorg/spirit/pull/259#discussion_r151292140  

Do you know why the `operator%=(const&,&)` and `operator%=(const&,&&)` need to exist? Can't they be removed and use the `operator%=(const&, const&)` version instead? It appears that the right hand side never needs to be modified.  
  
EDIT: comment says it right there! oh my.

> Username: Kojoley  
> Created_at: 2017-11-16 00:29:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/259#discussion_r151292827  

You will get what was before the commit - proto's `operator%=(&&, &&)` will be used and it is not what we want.


---
